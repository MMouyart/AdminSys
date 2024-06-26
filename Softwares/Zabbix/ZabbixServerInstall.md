# How to install and configure Zabbix for a simple monitoring scenario
First go to [the Zabbix download page](https://www.zabbix.com/download) and select your platform as well as the component you prefer.

In our example we will use debian 12 bookworm with apache and postgresql.

Here are the instructions to follow :
```bash
# Add the Zabbix deb file to our system
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-1+debian12_all.deb
# depackage it
dpkg -i zabbix-release_7.0-1+debian12_all.deb
# update apt cache
apt update
# install zabbix components
apt install zabbix-server-pgsql zabbix-frontend-php php8.2-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent
# install postgresql
apt install postgresql postgresql-contrib
# In postgres create a new role for zabbix (i.e. named zabbix), if you get an error saying user postgres does not have the autorisation to be in the current directory
# that is only because you are executing a command as user postgres in a directory that user postgres does not normally have access to, no need to worry
# remember the password for the postgres role zabbix (you will need it later)
sudo -u postgres createuser --pwprompt zabbix
# Still in postgres create a new database and add user zabbix as its owner
# when prompted for a password, use the password configured earlier for postgres role zabbix
sudo -u postgres createdb -O zabbix zabbix
# Then import and create the initial databse schema and data that are required for zabbix to work properly
# When prompted for a password enter the password you created for the postgres role zabbix
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
# With your favorite text editor open /etc/zabbix/zabbix_server.conf
# Search for the following lines and uncomment them :
# DBName =
# DBUser =
# DBPassword =
# For these lines, uncomment them and respectively enter :
# The zabbix database name (i.e. database zabbix)
# The zabbix database user to interact with it (i.e. user zabbix)
# The password of the user to interact with the zabbix database (i.e. the password you entered for the postgres role zabbix)
# Restart the required services
systemctl restart zabbix-server zabbix-agent apache2
# Enable those services to start at boot
systemctl enable zabbix-server zabbix-agent apache2
```

Once all the above steps have been completed, your zabbix server is ready to be configured via the web gui.

Go to http://<your zabbix server ip>/zabbix

Then start the configuration process

In the database configuration do the following : 
- Database type : select mysql or postgres given what you have installed
- Database host : the hostname of the server running the database, if it runs locally enter localhost
- Database port : leave as is
- Database name : enter zabbix
- Database user : enter zabbix
- Databse password : enter the password for the zabbix user

For now do not click on tls encryption if no ssl certificates have been created, otherwise do click it

In zabbix server details enter the following : 
- host : leave as default if your are running zabbix locally, otherwise enter the hostname of the server running zabbix
- port : leave as default if you have not configured zabbix server to use a different port

Note that this configuration process creates a file located at /usr/share/zabbix/conf/zabbix.conf.php which you can still modify later (for tls encryption among other things)
