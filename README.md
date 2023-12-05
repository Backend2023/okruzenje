# okruzenje
Algebra radno okruznje u PHP-u

- [markdown](https://www.markdownguide.org/cheat-sheet/)
- [virtualbox](https://www.oracle.com/virtualization/technologies/vm/downloads/virtualbox-downloads.html)
- [git](https://git-scm.com/download/win)
- [VS Code](https://code.visualstudio.com/docs/setup/windows)
- [Apache on Ubuntu22](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-22-04)


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







