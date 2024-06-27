# Installing zabbix agent
After the zabbix server service has coorectly been installed and configured, it is time to configure an agent which our zabbix server will communicate with.

This agent can either be your zabbix server (zabbix server monitoring itself) or another one.

We will proceed as follow :
- install the agent and configure it with clear text conmmunications
- configure the agent to encrypt communications using a psk
- configure the agent to encrypt communications using a certificate

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

