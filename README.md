# okruzenje
Algebra radno okruznje u PHP-u

- [markdown](https://www.markdownguide.org/cheat-sheet/)
- [virtualbox](https://www.oracle.com/virtualization/technologies/vm/downloads/virtualbox-downloads.html)
- [git](https://git-scm.com/download/win)
- [VS Code](https://code.visualstudio.com/docs/setup/windows)
- [Apache on Ubuntu22](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-22-04)
- [Install php8.1 on Ubuntu22](https://www.digitalocean.com/community/tutorials/how-to-install-php-8-1-and-set-up-a-local-development-environment-on-ubuntu-22-04)


## Virtualbox
- Instalirati
`  > new > type "linux", version "Ubuntu 22" > next > memory "4096", processors "2" `

### Postavke:
![Settings general](vbox_01_settings_general.png)
![settings storage](vbox_02_settings_storage.png)
![settings network](vbox_03_settings_network.png)
![settings shared](vbox_04_settings_shared.png)
![settings interface](vbox_05_settings_interface.png)
![settings guest](vbox_06_devices_guest_addition.png)

- IP adresa virtualnog stroja:
``` 
hostname -I
```
Vraća npr. : 192.168.199.118

- Pokrećemo stranice sa:
```
http://192.168.199.118:82/prve_stranice.php
```

Namjestiti port u /etc/apache2/ports.conf
```
Listen 82
```

Kreirati novi virtual host u: /etc/apache2/sites-available
```
sudo touch druga.conf
```

algebra@ubuntu22:/etc/apache2/sites-available$` cat druga.conf`
```
<VirtualHost *:82>
    ServerAdmin webmaster@localhost
    ServerName druga
    ServerAlias www.druga
    DocumentRoot /var/www/druga
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
**omogućiti virtualhost "druga"**

algebra@ubuntu22:/etc/apache2/sites-available$ `sudo a2ensite druga.conf`

**učitiati novu konfiguraciju:**

algebra@ubuntu22:/etc/apache2/sites-available$ `systemctl reload apache2`

**restartirati apache (ukoliko je potrebno)**

algebra@ubuntu22:/etc/apache2/sites-available$ `systemctl restart apache2`

**Izgled sheranog foldera "druga"**
```
algebra@ubuntu22:/var/www/druga$ ls -la

total 10
drwxrwx--- 1 root vboxsf 4096 Dec  5 11:16 .
drwxr-xr-x 5 root root   4096 Dec  4 23:20 ..
-rwxrwx--- 1 root vboxsf   17 Dec  4 20:03 phpinfo.php
-rwxrwx--- 1 root vboxsf    7 Dec  4 21:16 prve_stranice.php
drwxrwx--- 1 root vboxsf    0 Dec  4 22:42 t2
drwxrwx--- 1 root vboxsf    0 Dec  4 23:29 t3
drwxrwx--- 1 root vboxsf    0 Dec  5 10:42 t4
algebra@ubuntu22:/var/www/druga$ 
```
---

#sheranje direktorija u vboxu
- 
![novi share](vbox_07_shared_new_directory.png)

**Instalirati guest aditions kao sudo**
```
algebra@ubuntu22:/media/algebra/VBox_GAs_7.0.12$ pwd
/media/algebra/VBox_GAs_7.0.12

algebra@ubuntu22:/media/algebra/VBox_GAs_7.0.12$ sudo ./autorun.sh
```

**Postaviti korisnika algebra i www-data u grupu vboxsf**

```
algebra@ubuntu22:/var/www/druga$ sudo adduser www-data vboxsf
algebra@ubuntu22:/var/www/druga$ sudo usermod -a -G vboxsf www-data 
algebra@ubuntu22:/var/www/druga$ sudo adduser $USER vboxsf

algebra@ubuntu22:/var/www/druga$ cat /etc/group | grep vboxsf
vboxsf:x:999:algebra,www-data
```
# Postavke XAMPP

Kako dodati vanjski direktorij za hostanje na XAMPP

**C:\xampp\apache\conf\extra\httpd-vhosts.conf**
```
Listen 81
<VirtualHost *:81>
    ##ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "C:/xampp/htdocs/prva"
    ServerName prva
    ErrorLog "logs/prva.log"
    CustomLog "logs/prva.log" common
</VirtualHost>
```


**IP-based virtual hosting**
```
Listen 82
<VirtualHost prva.test:82>
    ##ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "C:/code/primjer_git"
    ServerName primjer
    ErrorLog "logs/primjer_git.log"
    CustomLog "logs/primjer_git.log" common
    <Directory "C:/code/primjer_git">
        AllowOverride All
        Options Indexes FollowSymLinks
        Require all granted 
    </Directory>
</VirtualHost>
```

**Name-based Virtual Hosting**
```
NameVirtualHost 127.0.0.3
<VirtualHost 127.0.0.3>
    ##ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "C:/code/primjer_git"
    ServerName prva3.test
    ErrorLog "logs/primjer_git.log"
    CustomLog "logs/primjer_git.log" common
    <Directory "C:/code/primjer_git">
AllowOverride All
        Options Indexes FollowSymLinks
        Require all granted 
    </Directory>
</VirtualHost>
```



# Postavke mariadb na ubuntu
**Instalacija**
https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-22-04


**KREIRAJ KORISNIKA KOJI NIJE LOCALHOST!!!**
```
CREATE USER 'mojroot'@'%' IDENTIFIED BY '123';
```

```
GRANT EXECUTE, PROCESS, SELECT, SHOW DATABASES, SHOW VIEW, ALTER, ALTER ROUTINE, CREATE, CREATE ROUTINE, CREATE TABLESPACE, CREATE TEMPORARY TABLES, CREATE VIEW, DELETE, DROP, EVENT, INDEX, INSERT, REFERENCES, TRIGGER, UPDATE, CREATE USER, FILE, LOCK TABLES, RELOAD, REPLICATION CLIENT, REPLICATION SLAVE, SHUTDOWN, SUPER  ON *.* TO 'mojroot'@'%' WITH GRANT OPTION;
```

```
FLUSH PRIVILEGES;
```

```
SHOW GRANTS FOR 'mojroot'@'%';
```


**DODAVANJE ROOT SA GRANT OPTION**

MariaDB [(none)]>```CREATE USER 'root'@'%' IDENTIFIED BY '123';```

Query OK, 0 rows affected (0.014 sec)


MariaDB [(none)]>``` GRANT ALL ON *.* TO 'root'@'%';```

Query OK, 0 rows affected (0.020 sec)



MariaDB [(none)]>``` FLUSH PRIVILEGES;```

Query OK, 0 rows affected (0.001 sec)



**DA BI PRISTUPILI MARIADB IZVAN VBOX-A**
```
algebra@ubuntu22:/etc/mysql/mariadb.conf.d$ pwd

/etc/mysql/mariadb.conf.d
algebra@ubuntu22:/etc/mysql/mariadb.conf.d$ sudo nano 50-server.cnf

Izmjeniti donja dva reda
#bind-address            = 127.0.0.1
bind-address            = 0.0.0.0
```

# Composer instalacija ubuntu22
**windows**
[https://getcomposer.org/download/](https://getcomposer.org/download/)

**Ubuntu22**
[https://www.digitalocean.com/community/tutorial(s/how-to-install-and-use-composer-on-ubuntu-22-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-22-04)



algebra@ubuntu22:/etc/mysql/mariadb.conf.d$ `php -v`

PHP 8.1.2-1ubuntu2.14 (cli) (built: Aug 18 2023 11:41:11) (NTS)

Copyright (c) The PHP Group

Zend Engine v4.1.2, Copyright (c) Zend Technologies

    with Zend OPcache v8.1.2-1ubuntu2.14, Copyright (c), by Zend Technologies

algebra@ubuntu22:/etc/mysql/mariadb.conf.d$ `composer -v`

Command 'composer' not found, but can be installed with:

sudo apt install composer

algebra@ubuntu22:/etc/mysql/mariadb.conf.d$ `sudo apt install php-cli unzip`

[sudo] password for algebra: 

Reading package lists... Done

Building dependency tree... Done

Reading state information... Done

unzip is already the newest version (6.0-26ubuntu3.1).

unzip set to manually installed.

The following NEW packages will be installed:

  php-cli

0 upgraded, 1 newly installed, 0 to remove and 35 not upgraded.

Need to get 3,234 B of archives.

After this operation, 25.6 kB of additional disk space will be used.

Do you want to continue? [Y/n] Y

Get:1 http://hr.archive.ubuntu.com/ubuntu jammy/main amd64 php-cli all 2:8.1+92ubuntu1 [3,234 B]

Fetched 3,234 B in 0s (19.7 kB/s)

Selecting previously unselected package php-cli.

(Reading database ... 208479 files and directories currently installed.)

Preparing to unpack .../php-cli_2%3a8.1+92ubuntu1_all.deb ...

Unpacking php-cli (2:8.1+92ubuntu1) ...

Setting up php-cli (2:8.1+92ubuntu1) ...

update-alternatives: using /usr/bin/php.default to provide /usr/bin/php (php) in auto mode

update-alternatives: using /usr/bin/phar.default to provide /usr/bin/phar (phar) in auto mode

update-alternatives: using /usr/bin/phar.phar.default to provide /usr/bin/phar.phar (phar.phar) in auto mode

Processing triggers for man-db (2.10.2-1) ...

algebra@ubuntu22:/etc/mysql/mariadb.conf.d$ `cd ~`

`curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php`

Command 'curl' not found, but can be installed with:

sudo snap install curl  # version 8.1.2, or

sudo apt  install curl  # version 7.81.0-1ubuntu1.14

See 'snap info curl' for additional versions.

algebra@ubuntu22:~$ `sudo apt  install curl  # version 7.81.0-1ubuntu1.14`

Reading package lists... Done

Building dependency tree... Done

Reading state information... Done

The following additional packages will be installed:

  libcurl4

The following NEW packages will be installed:

  curl

The following packages will be upgraded:

  libcurl4

1 upgraded, 1 newly installed, 0 to remove and 34 not upgraded.

Need to get 483 kB of archives.

After this operation, 454 kB of additional disk space will be used.

Do you want to continue? [Y/n]` y`

Get:1 http://hr.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libcurl4 amd64 7.81.0-1ubuntu1.15 [289 kB]

Get:2 http://hr.archive.ubuntu.com/ubuntu jammy-updates/main amd64 curl amd64 7.81.0-1ubuntu1.15 [194 kB]

Fetched 483 kB in 0s (1,152 kB/s)

(Reading database ... 208488 files and directories currently installed.)

Preparing to unpack .../libcurl4_7.81.0-1ubuntu1.15_amd64.deb ...

Unpacking libcurl4:amd64 (7.81.0-1ubuntu1.15) over (7.81.0-1ubuntu1.14) ...

Selecting previously unselected package curl.

Preparing to unpack .../curl_7.81.0-1ubuntu1.15_amd64.deb ...

Unpacking curl (7.81.0-1ubuntu1.15) ...

Setting up libcurl4:amd64 (7.81.0-1ubuntu1.15) ...

Setting up curl (7.81.0-1ubuntu1.15) ...

Processing triggers for man-db (2.10.2-1) ...

Processing triggers for libc-bin (2.35-0ubuntu3.4) ...

algebra@ubuntu22:~$ `curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php`

```
algebra@ubuntu22:~$ HASH=`curl -sS https://composer.github.io/installer.sig`
```

algebra@ubuntu22:~$ `echo $HASH`

e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02

algebra@ubuntu22:~$ `php -r "if (hash_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"`

Installer verified

algebra@ubuntu22:~$ `sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer`

All settings correct for using Composer

Downloading...



Composer (version 2.6.6) successfully installed to: /usr/local/bin/composer

**Use it: php /usr/local/bin/composer**



algebra@ubuntu22:~$ `composer`

   ______

  / ____/___  ____ ___  ____  ____  ________  _____

 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/

/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /

\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/

                    /_/




