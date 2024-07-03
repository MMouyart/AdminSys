# How to install and use prometheus to monitor a server
We will be using a debian 12 bookworm for the installation.

## Prometheus installation
Please, refer to the [latest version of prometheus](https://prometheus.io/download/) when following this tutorial for security reasons.
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz
tar xvzf prometheus-2.53.0.linux-amd64.tar.gz
# generate tls certificate and private key
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -keyout <prometheus key path> -out <prometheus certificate path> -subj "/C=FR/CN=<server fqdn>"
# create a file named web-config.yml and paste the following
tls_server_config:
  cert_file: <path to prometheus certificate>
  key_file: <path to prometheus private key>
# save and quit
# start a prometheus instance (by default it runs on port 9090) in the background
./prometheus \
  --config.file=<path to prometheus.yml config file> \
  --web.config.file=<path to web-config.yml tls config file> &
# test the connectivity to the web interface
curl --insecure https://localhost:9090/graph
# it should the graph html raw page
# when needed shutdown prometheus
kill -s SIGHUP <prometheus pid>
# or just kill it
kill -s SIGTERM <prometheus pid>
```

## Prometheus authentication
As of now, any body who can acces the prometheus server can basically view and do anything, to remediate to that let's implement a username and password authentication mechanism
```bash
# create a bcrypt hash for the specified user and password
htpasswd -nbBC 10 <user> <password>
# the result is of the form
USER:$2y$10$gfPbhclOrdjmMLEXSc5CTOWP5aBfjl59hhGvjp/qWXf1FfbQKb5ca
# the first part before the ":" is the username, the rest is the bcrypt hash, copy it
# modify the web configuration file of prometheus and add the following
basic_auth_users:
  <user>: <bcrypt hash>
# restart prometheus and now you shold be prompted for a username and password

```
## Monitor your linux server using node exporter
Please refer to the [latest version of node exporter](https://prometheus.io/download/#node_exporter) when following this tutorial for security reasons.
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.8.1.linux-amd64.tar.gz
# configure tls communications
# generate tls certificate and private key
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -keyout <node exporter key path> -out <node exporter certificate path> -subj "/C=FR/CN=<server fqdn>"
# create a file named web-config.yml and paste the following
tls_server_config:
  cert_file: <path to node exporter certificate>
  key_file: <path to node exporter private key>
# save and quit
# configure prometheus to collect node exporter metrics
sudo <editor of your choice> <path to prometheus.yml configuration file>
# add the following under scrape_configs
  - job_name: 'node-exporter'
    scheme: https
    tls_config:
      ca_file: <path to node exporter certificate>
    static_configs:
    - targets: ['localhost:9100']
# save and quit
# start node explorer
./node_exporter \
--web.config.file=<path to the web-config.yml file>
# via a web browser go to https://localhost:9090/targets
# you should see the target https://localhost:9100/metrics is up
```

## Setting up an authentication mechanism between prometheus and the node exporter instances
The goal of this is to allow metrics collection from prometheus only to allowed user, therefore preventing any unauthorised person to gather information about the linux server's metrics.
```bash
# create a bcrypt hash for the specified user and password
htpasswd -nbBC 10 <user> <password>
# the result is of the form
USER:$2y$10$gfPbhclOrdjmMLEXSc5CTOWP5aBfjl59hhGvjp/qWXf1FfbQKb5ca
# the first part before the ":" is the username, the rest is the bcrypt hash, copy it
# under the node exporter web config file add the following
basic_auth_users:
  <user>: <bcrypt hash>
# in the prometheus configuration file add the following under the node exporter job_name
    basic_auth:
      username: <node exporter username>
      password: <node exporter password>
# reload prometheus and in the https://localhost:9090/targets page you should see the target https://localhost:9100/metrics is up
```

## Setting up systemd services for prometheus and node exporter
To automate the process creation and restart of prometheus and node exporter, the best is to create systemd services (if your init system is systemd)
```bash
# first let's create the service for prometheus
sudo <editor of your choice> /etc/systemd/system/prometheus.service
# add the following
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target
[Service]
User=<user to launch prometheus with>
Group=<group of the user if needed to specify>
ExecStart=<path of the binary> --config.file="<path of the configuration file> --web.config.file="<path to the web configuration file>" --storage.tsdb.path="<path to the prometheus data folder>"
[Install]
WantedBy=multi-user.target
# save and exit
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo sytemctl start prometheus
# normally everything should run fine, otherwise troubleshoot to see what's wrong
# let's then create the node exporter service
sudo <editor of your choice> /etc/systemd/system/node_exporter.service
# add the following
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target prometheus.service
[Service]
User=<user to launch node exporter with>
Group=<group of the user if needed to specify>
ExecStart=<path of the binary> --web.config.file="<path to the web configuration file>"
[Install]
WantedBy=multi-user.target
# save and exit
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo sytemctl start prometheus
# normally everything should run fine, otherwise troubleshoot to see what's wrong
```
