# Installing zabbix agent
After the zabbix server service has coorectly been installed and configured, it is time to configure an agent which our zabbix server will communicate with.

This agent can either be your zabbix server (zabbix server monitoring itself) or another one.

We will proceed as follow :
- install the agent and configure it with clear text conmmunications
- configure the agent to encrypt communications using a psk
- configure the agent to encrypt communications using a certificate
- firewall considerations

## Installing zabbix agent and configure it for cleartext communications
First download the zabbix agent .deb file and unpack it
```bash
wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
sudo dpkg -i zabbix-release_5.0-1+focal_all.deb
```

Then update apt cache and install the zabbix agent package
```bash
apt update
apt install zabbix-agent
```
Then modify the agent configuration file to enable communications with the zabbix server
```bash
sudo <text editor of your choice> /etc/zabbix/zabbix_agentd.conf
# Find the section ### Option: Server
# Enter the statement Server with the zabbix server ip address
Server=<your zabbix server ip>
# Find the section ### Option: ServerActive
# Enter the statement ServerActive and add the zabix server id address
ServerActive=<zabbix server ip>
# save and quit
# restart the zabbix agent service
sudo systemctl restart zabbix-agent
```
In the zabbix server front-end, add a new host and specify its ip address or its fqdn (that can be resolved using dns).

## Configure  the zabbix agent to encrypt communications using a psk
First we need to create a psk, i basically is a stream of random hexadecimal numbers of any defined lenght. 
```bash
# to create our psk we will use openssl, use any key length you wish
sudo openssl rand -hex 32 > <path for your psk>
# output the psk value, and copy it, you will need it when modifying the encryption configurations for that host in the zabbix server front-end
cat <path for your psk>
```
Now we will modify our agent configuration to enable encrypted commumnications with that psk
```bash
sudo nano /etc/zabbix/zabbix_agentd.conf
# locate the section ### Option: TLSConnect
# enter the following
TLSConnect=psk
# locate the section ### Option: TLSAccept
# enter the following
TLSAccept=psk
# locate the section ### Option: TLSPSKIdentity
# enter 
TLSPSKIdentity=<your psk identifier, any name you wish>
# locate the section ### Option: TLSPSKFile
# enter
TLSPSKFile=<your psk path>
# save and quit
# restart the zabbix agent service
sudo systemctl restart zabbix-agent
```
If the zabbix agent service restarts correctly, head over the zabbix server front-end.

In the host section, click on the agent you have configured, in the encryption section :
- select PSK in the "connection to host" section
- select PSK in the "connection from host" section
- click on "change PSK"
- enter your key identifier in the "PSK identity" section
- enter the psk value in the "PSK" section
- update the configuration

## Configure the agent to encrypt communications using a certificate
Using certificates for communications is easier to manage when there are a large number of agents.

As zabbix does not support self encrypted certificates (for the server and for the agents), we will need to create our own ca which will then sign the server and agents certificates
```bash
# create our own ca and all the required files
# create a directory for storing all the ca related files
mkdir zabbix_ca
# create a key pair for the ca
openssl genrsa -aes256 -out <zabbix ca key> 4096
# create a certificate for the ca and sign it with the ca key pair
openssl req -x509 -new -key <zabbix ca key> -sha256 -days 3560 -out <zabbix ca crt>
```
Now that the ca has been created along with its key pair and signed certificate, let's create a certificate for our server which will be signed by the ca
```bash
# create a directory for storing the server certificate and key pair
mkdir zabbix_server_certs
# create a key pair for the server
openssl genrsa -aes256 -out <zabbix server key> 4096
# create a certificate for the server and sign it with the ca key pair
openssl req -new -key <zabbix server key> -out <zabbix server csr>
openssl x509 -req -in <zabbix server csr> -CA <zabbix ca crt> -CAkey <zabbix ca key> -CAcreateserial -out <zabbix server crt> -days 1460 -sha256
# edit the zabbix server config file to adapt it for tls certificate-based connections
sudo <editor of your choice> /etc/zabbix/zabbix_server.conf
# navigate to the section ### Option TLSCaFile
# enter the following statement
TLSCAFile=<path to the ca certificate>
# navigate to the section ### Option TLSCertFile
# enter the following statement
TLSCertFile=<path to the server certificate>
# navigate to the section ### Option TLSKeyFile
# enter the following statement
TLSKeyFile=<path to the server key pair>
# save and exit
# restart the zabbix server service
sudo systemctl restart zabbix-server
```
If no errors occur then it should be good. Let's move on the agent configuration.
```bash
# create a directory for storing the agent certificate and key pair
mkdir zabbix_agent_certs
# create a key pair for the agent
openssl genrsa -aes256 -out <zabbix agent key> 4096
# create a certificate for the agent and sign it with the ca key pair
openssl req -new -key <zabbix agent key> -out <zabbix agent csr>
openssl x509 -req -in <zabbix agent csr> -CA <zabbix ca crt> -CAkey <zabbix ca key> -CAcreateserial -out <zabbix agent crt> -days 1460 -sha256
# edit the zabbix agent config file to adapt it for tls certificate-based connections
sudo <editor of your choice> /etc/zabbix/zabbix_agentd.conf
# navigate to the section ### Option TLSAccept
# enter the following statement
TLSAccept=cert
# navigate to the section ### Option
# enter the following statement TLSConnect
TLSConnect=cert
# navigate to the section ### Option TLSCAFile
# enter the following statement
TLSCAFile=<path to the ca certificate>
# navigate to the section ### Option TLSCertFile
# enter the following statement
TLSCertFile=<path to the agent certificate>
# navigate to the section ### Option TLSKeyFile
# enter the following statement
TLSKeyFile=<path to the agent key pair>
# save and exit
# restart the zabbix agent service
sudo systemctl restart zabbix-agent
```
Now that the configuration is done go to the zabbix server front-end and modify the agent encryption configuration :
- select Certificate in the "connection to host" section
- select Certificate in the "connection from host" section
- in the issuer section enter the issuer information relative to the agent certificate in the form : CN= ,O= ,ST=,C=
- in the subject section enter the subject information relative to the agent certificate in the form : CN= ,O= ,ST=,C=

For the issuer and subject properties you can find them using the following command
```openssl x509 -noout -text -in <agent certificate>```

Suppose we have a certificate and the above command returns the following regarding the issuer and the subject :
- Issuer: C = FR, ST = Some-State, O = Internet Widgits Pty Ltd, CN = zabbix_ca
- Subject: C = FR, ST = Some-State, O = Internet Widgits Pty Ltd, CN = zabbix_agent

Then for the agent encryption configuration we would enter the following :
- "CN=zabbix_ca,O=Internet Widgits Pty Ltd,ST=Some-State,C=FR" for the issuer section 
- "CN=zabbix_ca,O=Internet Widgits Pty Ltd,ST=Some-State,C=FR" for the subject section

## Firewall considerations
When installing and configuring the agent on the machine, we create new network needs, which will require us to adapt our current firewall to allow zabbix realted traffic.

If you are not encrypting your communications with the zabbix server or not will not change the requirements.

To properly work, you will need to open port 10050 and allow communication on this port to the zabbix server.
In addition, if you wish to use the zabbix server front-end, you need to allow traffic to the server on port 80 (if the server does not use tls) or port 443 (if the server uses tls).
