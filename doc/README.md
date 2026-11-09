# Virtual Websites Tests

## 1. Adding Apolo and Atenea aliases

We add the Apolo and Atenea aliases to the olimpo.test.dns file. They should be added at the end of the file.

apolo     IN    CNAME    mercurio.olimpo.test.
atenea    IN    CNAME    mercurio.olimpo.test.

## 2. Adding zone checks

Inside the DNS provisioning script, we add named-checkconf -z before restarting the service. This command checks the Bind9 configuration and verifies that its DNS zones can be loaded.

cp -v /vagrant/files/ns1.olimpo.test/named /etc/default/
cp -v /vagrant/files/ns1.olimpo.test/named.conf.options /etc/bind
cp -v /vagrant/files/ns1.olimpo.test/named.conf.local /etc/bind
cp -v /vagrant/files/ns1.olimpo.test/olimpo.test.dns /var/lib/bind
cp -v /vagrant/files/ns1.olimpo.test/192.168.57.dns /var/lib/bind

systemctl restart named
systemctl status named


## 3. resolv.conf configuration

Here, we modify the resolv.conf files of ns1.olimpo.test and mercurio.olimpo.test, adding the following configuration:

search olimpo.test
nameserver 192.168.57.10


## 4. Apache provisioning

In the Apache provisioning script, we add the following commands.

#
# Copy Apache2 configuration files, including:
#
# - apache2.conf,
# - apolo.olimpo.test.conf
# - atenea.olimpo.test.conf
#

# Your code here
cp -v /vagrant/files/mercurio.olimpo.test/apolo.olimpo.test.conf /etc/apache2/sites-available/
cp -v /vagrant/files/mercurio.olimpo.test/atenea.olimpo.test.conf /etc/apache2/sites-available/


# Enable the virtual hosts
#

# Your code here
a2ensite apolo.olimpo.test.conf
a2ensite atenea.olimpo.test.conf

#
# Restart the service
#

# Your code here
systemctl restart apache2
systemctl status apache2


## 5. Configuring the Apolo and Atenea Virtual Hosts

Here, we configure a Virtual Host for atenea.olimpo.test on port 80 and another for apolo.olimpo.test, setting their document root directories. We also customize the 403 and 404 error pages, enable access and error logs, and configure directory permissions, disabling directory browsing where required.

For Atenea:

-Indexes: prevents directory listings from being displayed.

+FollowSymLinks: allows symbolic links to be followed.

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

For Apolo:

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

## 6. Creating the 404 messages

This will display the 404 error message on our websites.

We create one file for apolo.olimpo.test and another for atenea.olimpo.test. To do this, we create an HTML file called 404.html inside each website's messages directory and add the following content.

For Atenea:

<!DOCTYPE html>
<html>
<head>
    <title>404 Not Found</title>
</head>
<body>
    <h1>Resource not found on atenea.olimpo.test</h1>
</body>
</html>

For Apolo:

<!DOCTYPE html>
<html>
<head>
    <title>404 Not Found</title>
</head>
<body>
    <h1>Resource not found on apolo.olimpo.test</h1>
</body>
</html>

## 7. Checking DNS aliases

### For Apolo

To check the alias, we execute:

vagrant ssh dns -c "dig @192.168.57.10 apolo.olimpo.test CNAME +short"

PS C:\Users\User\Desktop\ASIRB2\sir\Web\Weblab-01-Virtual-websites> vagrant ssh dns -c "dig @192.168.57.10 apolo.olimpo.test CNAME +short"
mercurio.olimpo.test.

To check the IP address, we use:

vagrant ssh dns -c "dig @192.168.57.10 apolo.olimpo.test A +short"

PS C:\Users\User\Desktop\ASIRB2\sir\Web\Weblab-01-Virtual-websites> vagrant ssh dns -c "dig @192.168.57.10 apolo.olimpo.test A +short"
mercurio.olimpo.test.
192.168.57.11


### For Atenea

To check the alias, we execute:

vagrant ssh dns -c "dig @192.168.57.10 atenea.olimpo.test CNAME +short"

PS C:\Users\User\Desktop\ASIRB2\sir\Web\Weblab-01-Virtual-websites> vagrant ssh dns -c "dig @192.168.57.10 atenea.olimpo.test CNAME +short"
mercurio.olimpo.test.


To check the IP address, we use:

vagrant ssh dns -c "dig @192.168.57.10 atenea.olimpo.test A +short"

PS C:\Users\User\Desktop\ASIRB2\sir\Web\Weblab-01-Virtual-websites> vagrant ssh dns -c "dig @192.168.57.10 atenea.olimpo.test A +short"
mercurio.olimpo.test.
192.168.57.11


## 8. Browser tests
Now we will perform the tests in our browser. To do this, we must modify our computer's DNS configuration and set the IP address to 192.168.57.10 so that it can respond to the queries.
### For apolo

The file listing is displayed here:
`http://apolo.olimpo.test`
![alt text](img/img1.png)

The Apolo website is displayed here:
`http://apolo.olimpo.test/apolo.html`
![alt text](img/img2.png)

The 404 error is displayed here:
`http://apolo.olimpo.test`
![alt text](img/img3.png)

The 404 error is displayed here:
`http://apolo.olimpo.test`
![alt text](img/img4.png)

### Checking Apolo logs

To check the logs, we access the web machine and use the command sudo tail -n 20 /var/log/apache2/apolo.olimpo.test.access.log.
```
vagrant@mercurio:~$ sudo tail -n 20 /var/log/apache2/apolo.olimpo.test.access.log
192.168.57.1 - - [09/Nov/2026:18:31:49 +0000] "GET /messages/404.html HTTP/1.1" 200 468 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:31:49 +0000] "GET /favicon.ico HTTP/1.1" 404 444 "[http://apolo.olimpo.test/messages/404.html](http://apolo.olimpo.test/messages/404.html)" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:32:19 +0000] "GET / HTTP/1.1" 200 743 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:32:19 +0000] "GET /icons/blank.gif HTTP/1.1" 200 431 "[http://apolo.olimpo.test/](http://apolo.olimpo.test/)" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:32:19 +0000] "GET /icons/folder.gif HTTP/1.1" 200 509 "[http://apolo.olimpo.test/](http://apolo.olimpo.test/)" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:32:19 +0000] "GET /icons/text.gif HTTP/1.1" 200 513 "[http://apolo.olimpo.test/](http://apolo.olimpo.test/)" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:36:53 +0000] "GET /apolo.html HTTP/1.1" 200 304 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:39:42 +0000] "GET /index.html HTTP/1.1" 404 445 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:40:30 +0000] "GET /messages/404.html HTTP/1.1" 200 468 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
```
### For Atenea

The Atenea website is displayed here:
`http://atenea.olimpo.test/atenea.html`
![alt text](img/img5.png)

The custom 403 error is displayed here:
`http://atenea.olimpo.test`
![alt text](img/img6.png)

The custom 404 error is displayed here:
`http://atenea.olimpo.test/index.html`
![alt text](img/img7.png)

The custom 403 message is displayed here:
`http://atenea.olimpo.test/messages`
![alt text](img/img8.png)



[http://atenea.olimpo.test/messages](http://atenea.olimpo.test/messages)



### Checking Atenea logs

To check the logs, we access the web machine and use the command sudo tail -n 20 /var/log/apache2/atenea.olimpo.test.access.log.
```
vagrant@mercurio:~$ sudo tail -n 20 /var/log/apache2/atenea.olimpo.test.access.log
192.168.57.1 - - [09/Nov/2026:18:58:13 +0000] "GET /atenea.html HTTP/1.1" 200 305 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:58:13 +0000] "GET /favicon.ico HTTP/1.1" 404 445 "[http://atenea.olimpo.test/atenea.html](http://atenea.olimpo.test/atenea.html)" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:18:59:47 +0000] "GET / HTTP/1.1" 403 271 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:19:00:47 +0000] "GET /index.html HTTP/1.1" 404 446 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:19:02:12 +0000] "GET /messages HTTP/1.1" 301 639 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36"
192.168.57.1 - - [09/Nov/2026:19:02:12 +0000] "GET /messages/ HTTP/1.1" 403 270 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157
```