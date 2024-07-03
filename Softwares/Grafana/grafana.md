# Installing and using grafana for monitoring
Grafana is useful for monitoring your infrastructure. In this case a linux server monitored using prometheus.

## Install grafana
```bash
# in this scenario we will be using a debian 12 bookworm server
sudo apt-get install -y apt-transport-https software-properties-common wget
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana
# enable and start the service
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

## Modify grafana to use tls communications
First let's create our self-signed certificates and keys
```bash
sudo openssl genrsa -out /etc/grafana/grafana.key 4096
sudo openssl req -new -key /etc/grafana/grafana.key -out /etc/grafana/grafana.csr
sudo openssl x509 -req -days 365 -in /etc/grafana/grafana.csr -signkey /etc/grafana/grafana.key -out /etc/grafana/grafana.crt
sudo chown grafana:grafana /etc/grafana/grafana.crt
sudo chown grafana:grafana /etc/grafana/grafana.key
sudo chmod 400 /etc/grafana/grafana.key /etc/grafana/grafana.crt
# configure grafana for tls commmunications
# modify grafana configuration file
sudo <editor of your choice> /etc/grafana/grafana.ini
# under the server section add the following
domain = <your fqdn or hostname>
cert_key = /etc/grafana/grafana.key
cert_file = /etc/grafana/grafana.crt
protocol = https
# save and exit
# restart the service
sudo systemctl restart grafana-server
```

Using a browser go to https://<your server fqdn or hostname>:3000

Enter the default credentials : 
- username = admin
- password = admin

Change the default password.

## Adding a prometheus data source
At this point grafana is configured and a new source can be added to be monitored.

In the web ui, navigate to data sources, add a new one and click on prometheus data source.

In the settings enter the following :
- prometheus server url (under connection) --> enter the server fqdn or hostname
- under authentication choose basic authentication and fill in prometheus username and password
- if using a self-signed certificate for prometheus ; under tls settings, click add self-signed certificate
  - ca certificate --> fill in the raw value of prometheus certificate
  - click on skip tls certificate verification
- if using third party signed certificate ; under tls settings, click tls client authentication
  - servername --> fill in the server fqdn or hostname
  - client certificate --> fill in the raw prometheus certificate
  - client key --> fill in the raw prometheus key
Click save & test.

To add a dashboard for visualising the server metrics :
- in the dashboard section click new
- select import
- in the id section enter 1860
- load the dashboard
