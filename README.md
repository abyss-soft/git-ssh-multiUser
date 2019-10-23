# Работа c GitHub по SSH-ключам

### Генерация ключей 
Для начала посмотрим, какие ssh-ключи уже есть в системе:
`$ ls -al ~/.ssh`

Ключей еще нет, директория пуста. Сгенерируем ключи командой 
` ssh-keygen -t rsa `

****
```
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Alexander/.ssh/id_rsa):**вводим название файла с будущеим ключом, например git-ssh**
Enter passphrase (empty for no passphrase):**[Вводим пароль]**
Enter same passphrase again:**[Вводим пароль]**
Your identification has been saved in git-ssh.
Your public key has been saved in git-ssh.pub.
```
 Название и расположение файла для сохранения ключа можно указывать для удобства. Можно и  просто нжать Enter, чтобы сохранить ключ в файле по умолчанию (~/.ssh/id_rsa). Затем надо дважды ввести пароль (который можно оставить пустым, чтобы потом не вводить каждый раз, да, это безопасно).
****

У нас появились 2 файла ключа:

	git-ssh — приватный, который НЕЛЬЗЯ никому сообщать
	git-ssh.pub — публичный, который мы сообщим gitbucket и github


### Добавление ssh-ключа на bitbucket

Авторизуемся, заходим в свой аккаунт. Нажимаем SSH keys -> Add Key. После ввода ключа (т.е. вставляем сюда всё содержимое файла ~/.ssh/git-ssh.pub) нажимаем кнопку Add key для сохранения ssh-ключа.

### Добавление ssh-ключа на github

Теперь надо добавить наш ключ в аккаунте GitHub. Авторизуемся в своём профиле, нажимаем Edit Profile -> SSH Keys -> Add SSH key (или [https://github.com/settings/keys](https://github.com/settings/keys) ). После этого указываем title (это название ключа, вводится для удобства) и вставляем всё содержимое файла ~/.ssh/git-ssh.pub в текстовое поле «Key». Нажимаем Add key.

**Теперь можно использовать SSH вместо HTTPS**

## Авторизация по ssh-ключам на одном сервере под разными учетными записями

Если вы создали несколько учётных записей на одном сервере, предоставляющем доступ к git-репозиториям по ssh-ключам (сервисы github.com, bitbucket.org и др.), то и авторизоваться на этом сервере нужно с помощью разных ssh-ключей.

###### Например нам нужно сделать авторизацию на сервере bitbucket.org для двух пользователей

1. создадим ключи для авторизации:

первый:
```
	$ ssh-keygen -t rsa ~/.ssh/bitbucket1
	Your identification has been saved in ~/.ssh/bitbucket1
	Your public key has been saved in ~/.ssh/bitbucket1.pub
```

второй:
```
$ ssh-keygen -t rsa ~/.ssh/bitbucket2
Your identification has been saved in ~/.ssh/bitbucket2
Your public key has been saved in ~/.ssh/bitbucket2.pub
```

Теперь,чтобы обращаться к одному и тому же серверу bitbucket.org под разными ключами, нужно   в **~/.ssh/config** добавить две секции:

```
host bitbucket1
hostname bitbucket.org
user git
identityfile C:\Users\Alexander\.ssh\bitbucket1

host bitbucket2
hostname bitbucket.org
user git
identityfile C:\Users\Alexander\.ssh\bitbucket2
```


###### Проверим, есть ли у нас доступ:
$ ssh -T bitbucket1
You can use git or hg to connect to Bitbucket. Shell access is disabled
$ ssh -T bitbucket2
You can use git or hg to connect to Bitbucket. Shell access is disabled

Все отлично, доступ есть.

###### Далее, чтобы в  использовать эту адресацию с помощью программы git, надо просто заменять в url-ах строку git@bitbucket.org на соответствующую строку (которую вы указали в строке с директивой host выше).

Например, для клонирования репозитория git@bitbucket.org:angeldance/autologin.git от имени учётной записи bitbucket1:

`$ git clone bitbucket1:angeldance/autologin.git`

...от имени учётной записи bitbucket2:

`$ git clone bitbucket2:angeldance/autologin.git`


###### Для github.com все аналогично