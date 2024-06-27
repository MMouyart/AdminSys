# Using TLS to secure http connections with zabbix server front-end
Altough for now eveything works well, we need to secure our connections. 
The first connection we must secure is the http (which will now become https) connections to our zabbix server front-end.

The steps we will follow are :
- create a self-signed certificate
- configure apache to use this certificate for tls connections
- verify the connectivity
- configure apache to use https by default

## Create a self-signed certificate
Fow now we will proceed with a self-signed certificate (only if your server does not run publicly otherwise it is a heavy risk) first which we will later replace by a certificate signed by a third party CA.
```bash
# Proceed to verify the installation of openssl and if not installed already, install it
openssl -v
# if the command openssl is not found, install it with apt
apt install openssl
# create the public/private key pair, the CSR and the self-signed certificate
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-private-key.key -out /etc/ssl/certs/apache-self-signed-certificate.crt
# in the certificate creation process (i.e. interactive process) when prompted for a common name enter your zabbix server fqdn
# if your are running zabbix server locally, enter your server's hostname (you can use hostnamectl to find what your server's hostname is)
```
## Configure apache to use tls
```bash
# first we will modify the ssl parameters for apache
# create a new file called ss-params.conf
sudo <editor of your choice like nano, vi...> /etc/apache2/conf-available/ssl-params.conf
# in this newly created file, paste the following
SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
SSLHonorCipherOrder On
Header always set X-Frame-Options DENY
Header always set X-Content-Type-Options nosniff
SSLCompression off
SSLUseStapling on
SSLStaplingCache "shmcb:logs/stapling-cache(150000)"
SSLSessionTickets Off
# save and quit
# next we will modify the apache default virtual host file
# to enable rollback if any mistakes are made, first create a backup file for the default virtual host configuration
sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf.bak
# then edit the file (the .bak file can always be used to replace the configurations made if they do not work)
sudo <editor of your choice like nano, vi...> /etc/apache2/sites-available/default-ssl.conf
# in this file, we will make minor changes
# at the ServerMail statement, change the default value by your server mail (if you do not have one then skip)
# create a statement ServerName and enter your server's fqdn (if running locally enter your server's hostname)
ServerName <server's fqdn or hostname>
# at the SSLCertificateFile, modify the default value with the path of your self-signed certificate
SSLCertificateFile <your certificate path>
# at the SSLCertificateKeyFile, modify the default value with the path of your private key
SSLCertificateKeyFile <private key path>
# enable the use of apache modules for tls connections
sudo a2enmod ssl
sudo a2enmod headers
sudo a2enconf ssl-params
# verify your configuration is correct (a typo can easily occur)
sudo apache2ctl configtest
# if the message Syntax OK appears then you can restart apache2 to test connectivity
sudo systemctl restart apache2
```
# Test connectivity
From now on, https connections (using tls) as well as http connections are both accepted by the zabbix server.
Check the http connections ```wget http://<your server's fqdn/hostname>```, it should return something.

Check for https connections ```wget https://<your server's fqdn/hostname>```, it should return error messages related to the certificate being self-signed (which we created so that's ok).

Check https connections via a web browser, when the warning message about insecure web site appears, click continue and you should view the default web page.

If you encounter any problems, double check the server's hostname you have used throughout the certificate creation process and make sure it matches with the correct one.

# Configure apache to use https by default
At this point we have acknowledged that https connections do work and we want to disable http connections (i.e. not encrypted via tls).
```bash
# first modify the apache virtual host default configuration file to enable a permanent redirection to https connections
sudo <editor of your choice like nano, vi...> /etc/apache2/sites-available/000-default.conf
# Enter the following statement
Redirect permanent "/" "https://<server's fqdn or hostname>/"
# enable the use of tls by default at the apache configuration level
sudo a2ensite default-ssl
# check your configuration (typo occurs easily)
sudo apache2ctl configtest
# restart apache service
sudo systemctl restart apache2
```
Try to connect to your web server using http, it should automatically be redirected to https.

# Using a certificate signed by a trusted CA
First go to your prefered 3rd party CA to create a certificate.

Eventually, if the 3rd party CA allow you to sign a certificate you have created, proceed to create your certificate signing request with openssl.
```sudo openssl req -new -newkey rsa:2048 -nodes -keyout <path to your public/private key pair> -out <path to  your certificate signing request>```
Then use this csr to obtained a signed certificate by your 3rd party CA.

Copy on your server the certificate and the intermediate certificate.

Once you have obtained a signed certificate for your server, modify the apache virtual host default configuration file.
```bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
# enter the following
SSLCertificateFile <path to certificate>
SSLCertificateKeyFile <path to private key>
SSLCertificateChainFile <path to intermediate certificate>
# save and quit
# restart apache service
sudo systemctl restart apache2
```
Now when you connect to your zabbix server's front end, no warning message should be appearing.
