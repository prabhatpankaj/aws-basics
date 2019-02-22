# wordpress_ubuntu

- sudo apt-get install -y build-essential
- sudo apt-get install lamp-server^
- sudo apt-get install phpmyadmin
- sudo nano /etc/apache2/mods-enabled/dir.conf

update as 
```
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

- sudo systemctl restart apache2
- sudo nano /etc/apache2/apache2.conf
add the line at bottom as 
- Include /etc/phpmyadmin/apache.conf
update this
```
<Directory /var/www/>
        AllowOverride All
</Directory>
```
- sudo a2enmod rewrite
- sudo service apache2 restart

cd /var/www/

- curl -O https://wordpress.org/latest.tar.gz
- tar xzvf latest.tar.gz
- touch wordpress/.htaccess
- chmod 660 wordpress/.htaccess
- cp wordpress/wp-config-sample.php wordpress/wp-config.php
- mkdir wordpress/wp-content/upgrade
- sudo cp -a wordpress/. /var/www
- sudo chown -R www-data:www-data /var/www
- sudo find /var/www -type d -exec chmod g+s {} \;
- sudo chmod -R g+w /var/www/wp-content/themes
- sudo chmod -R g+w /var/www/wp-content/plugins
- curl -s https://api.wordpress.org/secret-key/1.1/salt/
- nano /var/www/wp-config.php
update generated salt from curl -s https://api.wordpress.org/secret-key/1.1/salt/
```
define('AUTH_KEY',         'e;wPq) x<G06Q_)Ni^MYPKJ2gh$WgJeu:Zpw[HFg./DD>,p.0BkU?+K$*Vj.e!z<');
define('SECURE_AUTH_KEY',  '+fr._?DT:fJ5-?Cq4PJK;-0C|{WsA(_MP>LHnsw?K26)QEr<_&yKz!ns>~Pf=.Fv');
define('LOGGED_IN_KEY',    '+{,5DVBKnn=G[qrY:M~9rrL$A?Oe:kc8$KEL~G^x=ubQ$vr4tRLZE#!s_0rC/N9G');
define('NONCE_KEY',        '!}Bi<j).+;g7Zn/cfstJHy)|QddGvI/ ,Imi~3mWPXQ>pZe)k,%*k.Inn<Zy4@vY');
define('AUTH_SALT',        '`M?5|2`[h+M@-u[%Scn|j;oJH7J`qe8So$KC2 [|7^vkI;LF=>e4g13y_c?@]9f2');
define('SECURE_AUTH_SALT', '2mg D#o+y0-j8#F`A~-(4=f`Q`-%u-i@$QoDX7~~-fnTx<.8KA~:)zNtCph(!rxV');
define('LOGGED_IN_SALT',   'J@Q-C v.lGYkQ[5Q`lY@%Ct&97pZ6D2Pz){~^{ZIU^%A|4E=0.->|s2]8,-|6}8-');
define('NONCE_SALT',       'g=}cih57|05#[B^cG7KC}<LmZB4jEKW-~=jef>po2+2IP+!vqh4w.OqTWeUE*bwO');
```
update database name , user and password 

- nano /etc/apache2/sites-available/000-default.conf
and update 
```
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www
    <Directory />
       Options FollowSymLinks
       AllowOverride None
       Require all denied
    </Directory>
    <Directory /var/www/>
       Options Indexes FollowSymLinks
       AllowOverride None
       Require all granted
    </Directory>
```



