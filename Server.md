# SERVER SETUP

- [Securing Server](#securing-server)
- [Create user](#create-user)
- [Configure ssh server](#configure-ssh-server)
- [Setup firewall](#setup-firewall)
- [Transferring Files](#transferring-files)
- [Install App](#install-apps)
	- [Nginx](#nginx)
	- [PHP](#php)
	- [MySQL](#mysql)
	- [Redis](#redis) 
	- [Tools](#tools)

- [Troubleshoots](#troubleshoots)


#### Securing Server
***
> Update & upgrade
> Create a non-root user
> Setup SSH Key authentication
> Disable password login

#### Create User

|- Create admin user
***
```
$ adduser username
# or
$ sudo useradd -d /home/adminname -m adminname
# set password
$ sudo passwd adminname
# add user to admin group
$ sudo adduser adminname sudo

```
|- Copy `adminuser`'s `ssh-key`

From local computer type: (must have ssh keys .ssh folders of your home directory)

```
$ ssh-copy-id adminname@server-ip-or-host
# now you can login using ssh keys
# you may be prompted to enter adminname password on first attempt
$ ssh adminname@host-or-ip

```
after login if the terminal only shows a `$` sign, install `bash` by using the following command

```
$ sudo chsh -s /bin/bash adminname

```
***you may need to logout and login again to see changes

|- Create `www` user & copy it's ssh key (see above)

#### Configure SSH Server
***
> Eedit `/etc/ssh/sshd_config` &
	- set listen address to server IP Address
	- PasswordAuthentication: no
	- & finally add the following two lines
```
	# User who can login via ssh
	AllowUsers nasim www-fardous
```


#### Setup Firewall
***

```
> sudo ufw disable
> sudo ufw default deny
> sudo ufw allow ssh
> sudo ufw enable
```

#### Transferring Files
***
- Using `scp`
```
# local to remote
$ scp file.zip user@host:/path/
#remove to local
$ scp user@host:/path/file.zip ~/home/path/
```
- Mount remote folder using `sshfs`
```
# install `sshfs`
suod apt install sshfs
# create mound dir
mkdir /home/remote-dir
# mount
sshfs user@host:~/ /home/remote-dir
# unmount
umount /home/remote-dir
```


#### Install Apps
***
>[LEMP Stack](#lemp-stack)
>[Redis](#redis) 
> [Tools](#tools)

#### | LEMP Stack

[DigitalOcean Guide](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-in-ubuntu-16-04)

#### Nginx

```
$ sudo apt install nginx
$ sudo ufw allow 'Nginx HTTP'
$ sudo ufw status

```
- Type server `ip address` in browser to see if 	`nginx` working

- Configure Nginx

	- Edit `/etc/nginx/nginx.conf`
	- Replace `nginx.conf` with [this file](nginx.conf)
	
#### MySql

```
$ sudo apt install mysql-server
$ mysql_secure_installation
```
> Remove anonymous users? ``yes``
> Disallow root login remotely? `yes`

- Edit `/etc/mysql/mysql.conf.d/mysqld.cnf` & at the bottom add

```
# custom config
ft_min_word_len=2
innodb-ft-min-token-size=2
bulk_insert_buffer_size=64M
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```

#### PHP

- Install PHP

```
$ sudo apt install php-fpm php-mysql
$ php -v
```

- Edit `/etc/php/7.0/fpm/php.ini` and change the following
```
cgi.fix_pathinfo=0
# increase pool
pm.max_children = 25
```
- Restart PHP-FPM
```
$ sudo systemctl restart php7.0-fpm
```
- Enable PHP on Nginx
-- Edit `/etc/nginx/sites-available/default` & add

```
...
index index.php ...
...
location ~ \.php$ {
	include snippets/fastcgi-php.conf;
	fastcgi_pass unix:/run/php/php7.0-fpm.sock;
}
```

- Test Nginx Configuration file &  restart

```
$ sudo nginx -t
$ sudo systemctl reload nginx
```

- Create file `/var/www/html/info.php` & write
```
<?php phpinfo(); ?>
```

- Test `info.php`
- List install PHP extensions
```
$ php -m
```
- Install following extension & restart `php-fpm`
> mbstring
> mcrypt
> curl
> php-xml
> zip
> gd
> imagick
> igbinary
>  redis

#### REDIS

- Add `PPA` & install Redis

```
$ sudo add-apt-repository ppa:chris-lea/redis-server
$ sudo apt-get update
$ sudo apt install redis-server
$ redis-cli
```

#### TOOLS
```
# curl
$ sudo apt install curl

# imagick
$ sudo apt install imagemagick
```

**Server spped test**
```
wget -O /dev/null http://cachefly.cachefly.net/100mb.test
```
**Generate SSH Key**
```
$ ssh-keygen
# when prompted enter passphrase

# now copy they key over to remote server
ssh-copy-id username@remotehost
```

### Usefull commands
- system
```
# free ram
$ free -h
# process list
$ ps
$ top

# server ip
$ hostname -I

# set timezone
$ dpkg-reconfigure tzdata

# network services running
sudo netstat -tulpn

# ssh tunnels
ssh user@example.com -L 3306:127.0.0.1:3306 -N
# now to connect mysql use
$ mysql -u root -p --host 127.0.0.1 --port 2206

# compress and copy file from remote computer
$ ssh user@server "tar czf - matrimony/" > matrimony.tgz
```

# mysql
```
# runninng threads
msyql > SHOW STATUS WHERE `variable_name` = 'Threads_connected';
# get overall status
msyql > status;

```
