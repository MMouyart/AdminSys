# For vsftpd without tls encryption : 
## Install vsftpd
Refer to your OS prefered package manager.
Example for Ubuntu distros.
```apt-get -y install vsftpd openssl```
<br>
Copy the default conf file :  ```mv vsftpd.conf vsftpd.conf.default```
<br>
edit conf file : 
```bash
listen=NO
listen_ipv6=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
pam_service_name=vsftpd
```
Restrict conf file permission : ```chmod 600 /etc/vsftpd/vsftpd.conf```
<br>
Create a FTP dedicated user : 
```bash
adduser ftpuser 
chown root:root /home/ftpuser
mkdir /home/ftpuser/files
chown ftpuser:ftpuser /home/ftpuser/files
```
Restart FTP server and check its status : 
```bash
systemctl restart vsftpd
systemctl enable vsftpd 
systemctl status vsftpd
```
If vsftpd encountered errors in configuration, check the origin : 
```/usr/sbin/vsftpd /etc/vsftpd.conf\n```
<br>
If vsftpd runs correctly, connect to it using any ftp client.

## For SFTP using TLS
After the installation part.
<br>
Create new ssl key : 
```openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt```
Edit vsftpd config file : 
```bash
ssl_enable=YES
force_dot_files=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=YES
ssl_ciphers=HIGH
rsa_cert_file=/etc/ssl/certs/vsftpd.crt
rsa_private_key_file=/etc/ssl/private/vsftpd.key
```
Restart vsftpd and check anny error, if any check the source
<br>
Connect to the FTP server using a client that support TLS tunneling
<br>
In lftp because the certificate is auto-signed do so : 
```bash
lftp <user>@<ip adress>
set ssl:verify-certificate no
```
Vsftpd man page link : https://linux.die.net/man/8/vsftpd
