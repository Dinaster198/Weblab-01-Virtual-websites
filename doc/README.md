# Pruebas  Virtual websites



## 1.Añadir alias de apolo 
Añadimos el alias de apolo en el fichero olimpo.test.dns. Alfinal del archivo se añadiria.

```
apolo    IN    CNAME    mercurio.olimpo.test.

```

## 2. Añadir comprobacion zonas
Dentro del dns provision añadimos antes del reinicio `named-checkconf -z` esto hara una comprobacion de la configuracion de Bind9 y de que sus zonas DNS puedan cargarse.

```
cp -v /vagrant/files/ns1.olimpo.test/named /etc/default/
cp -v /vagrant/files/ns1.olimpo.test/named.conf.options /etc/bind
cp -v /vagrant/files/ns1.olimpo.test/named.conf.local /etc/bind
cp -v /vagrant/files/ns1.olimpo.test/olimpo.test.dns /var/lib/bind
cp -v /vagrant/files/ns1.olimpo.test/192.168.57.dns /var/lib/bind

systemctl restart named
systemctl status named

```
## 3. Configuracion resolv.conf
Aqui modificamos los resolv.conf de ns1.olimpo.dns y de mercurio.olimpo.test y le añadimos lo siguiente:

```
search olimpo.test
nameserver 192.168.57.10

```

## 4. Apache provision
En la provision del apache rellenamos con lo siguiente.
```
# Copiar ficheros de configuración de apache2 que incluyen:
#
# - apache2.conf,
# - apolo.olimpo.test.conf
# - atenea.olimpo.test.conf
#

# tu código aquí
cp -v /vagrant/files/mercurio.olimpo.test/apolo.olimpo.test.conf /etc/apache2/sites-available/
cp -v /vagrant/files/mercurio.olimpo.test/atenea.olimpo.test.conf /etc/apache2/sites-available/


# Habilitar los servidores virtuales
#

# tu código aquí
a2ensite apolo.olimpo.test.conf
a2ensite atenea.olimpo.test.conf

#
# Reiniciar el servicio

# tu código aquí
systemctl restart apache2
systemctl status apache2

```

## Configuracion del virtualhost de atenea y apolo
Aqui configuraremos un VirtualHost para atenea.olimpo.test en el puerto 80 y para apolo.olimpo.test, estableciendo sus directorios raíz . También se personalizaremos las páginas de error 403 y 404,habilitamos los registros de acceso y errores y  configuramos los permisos del directorio, desactivando la exploración de directorios .

Para atenea:
```
<VirtualHost *:80>

    ServerName atenea.olimpo.test

    DocumentRoot /var/www/atenea.olimpo.test

    ErrorDocument 403 "You do not have permission to access atenea.olimpo.test"
    ErrorDocument 404 /messages/404.html

    ErrorLog ${APACHE_LOG_DIR}/atenea.olimpo.test.error.log
    CustomLog ${APACHE_LOG_DIR}/atenea.olimpo.test.access.log combined

    <Directory /var/www/atenea.olimpo.test>
        Options -Indexes +FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

</VirtualHost>
```
Para apolo:
```
<VirtualHost *:80>

    ServerName apolo.olimpo.test

    DocumentRoot /var/www/apolo.olimpo.test

    ErrorDocument 403 "You do not have permission to access apolo.olimpo.test"
    ErrorDocument 404 /messages/404.html

    ErrorLog ${APACHE_LOG_DIR}/apolo.olimpo.test.error.log
    CustomLog ${APACHE_LOG_DIR}/apolo.olimpo.test.access.log combined

    <Directory /var/www/apolo.olimpo.test>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

</VirtualHost>
```