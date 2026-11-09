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

named-checkconf -z
systemctl restart named
systemctl status named

```
## 3. Configuracion resolv.conf
Aqui añadimos la ip de la maquina para que de respuesta.

```
domain olimpo.test
search olimpo.test
nameserver 192.168.57.10
nameserver 127.0.0.1
nameserver 10.0.2.3


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
cp -v /vagrant/flies/mercurio.olimpo.test/apolo.olimpo.test.conf /etc/apache2/sites-available/
cp -v /vagrant/flies/mercurio.olimpo.test/atenea.olimpo.test.conf /etc/apache2/sites-available/


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