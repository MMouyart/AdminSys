# How to install and use prometheus to monitor a server
We will be using a debian 12 bookworm for the installation.

## Prometheus installation
Please, refer to the [latest version of prometheus](https://prometheus.io/download/) when following this tutorial for security reasons.
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz
tar xvzf prometheus-2.53.0.linux-amd64.tar.gz
# generate tls certificate and private key
openssl req -x509 -newkey rsa:4096 -nodes -keyout <prometheus key path> -out <prometheus certificate path>
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
kill -s SIGTERM <prometheus pid>x
```

## Monitor your linux server using node exporter
Please refer to the [latest version of node exporter](https://prometheus.io/download/#node_exporter) when following this tutorial for security reasons.
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.8.1.linux-amd64.tar.gz
# configure tls communications
# generate tls certificate and private key
openssl req -x509 -newkey rsa:4096 -nodes -keyout <node exporter key path> -out <node exporter certificate path>
# create a file named web-config.yml and paste the following
tls_server_config:
  cert_file: <path to node exporter certificate>
  key_file: <path to node exporter private key>
# save and quit
# configure prometheus to collect node exporter metrics
sudo <editor of your choice> <path to prometheus.yml configuration file>
# in the target line, inside the brackets add the following
localhost:9100
# save and quit
# start node explorer
./node_exporter \
--web.config.file=<path to the web-config.yml file>
