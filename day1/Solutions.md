DAY 1 Monitoring

The Assignment
You have to perform this assignment by creating roles for exporter and server.
Exporters
MySQL Exporter 
Node Exporter 
Nginx Exporter 
MongoDB Exporter 
Collectd Exporter 
Jenkins Exporter 
Prometheus
Install prometheus server on your vagrant machine or vm. 
Configure another machine and install node exporter on it and monitor it with prometheus. 
Install MySQL or Mongo on that node and monitor MySQL or Mongo with prometheus. 
Install Apache or Nginx on that node and monitor Apache or Nginx with prometheus. 
Install Collectd and Jenkins exporter and monitor it with prometheus. 
Alerting
Create alert if node is down for more than 2 min 
Create alert if cpu utilization of node more than 80% 
Create alert if mysql or mongo scrape error is above than 1 
Create alert if mysql connection error or mongo connection error is above than 1 
Create alert if jenkins job is failing more than 1 time. 



```
Alert manager – 192.168.0.18
Node exporter – 192.168.0.4
Prometheus – 192.168.0.94
```

```
Prometheus_port - 9100
Node_exporter   - 9090
alert_manager   - 9300
nginx_exporter  - 9913
mysql_exporter  - 9104
jenkins_exporter- 9118
collectd_exporter- 9103
```
```
#######CREATING EXPORTERS MANUALLY#######
```

Creating mysql exporter

DOWNLOAD A BINARY
- wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.10.0/mysqld_exporter-0.10.0.linux-amd64.tar.gz

INSTALLING

- tar xvzf mysqld_exporter-0.10.0.linux-amd64.tar.gz

- sudo adduser --no-create-home --disabled-login --shell /bin/false --gecos "MySQL exporter user" mysql_exporter

- sudo mkdir /etc/mysql_exporter

- sudo touch /etc/mysql_exporter/.my.cnf

- sudo chown -R mysql_exporter:mysql_exporter /etc/mysql_exporter

- sudo chmod 600 /etc/mysql_exporter/.my.cnf

- sudo cp mysqld_exporter-0.10.0.linux-amd64/mysqld_exporter /usr/local/bin/

- sudo chown mysql_exporter:mysql_exporter /usr/local/bin/mysqld_exporter


CONFIGURING .my.cnf

- sudo nano /etc/mysql_exporter/.my.cnf
[client]
user=vagrant
password=123

CREATING SERVICE FOR MYSQL-EXPORTER

- sudo nano /etc/systemd/system/mysql_exporter.service
[Unit]
Description=Prometheus MySQL Exporter
After=network.target

[Service]
User=mysql_exporter
Group=mysql_exporter
Type=simple
ExecStart=/usr/local/bin/mysqld_exporter \
    --config.my-cnf="/etc/mysql_exporter/.my.cnf"
Restart=always

[Install]
WantedBy=multi-user.target

- sudo systemctl daemon-reload

- sudo systemctl enable mysql_exporter

- sudo systemctl start mysql_exporter

```
#######Creating Node Exporter#######
```

DOWNLOAD A BINARY

- wget https://github.com/prometheus/node_exporter/releases/download/v0.15.1/node_exporter-0.15.1.linux-amd64.tar.gz


INSTALLING

- mkdir ~/vagrant/node_exporter

- cd ~/vagrant/node_exporter

- tar -xzvf ~/Downloads/node_exporter-0.15.1.linux-amd64.tar.gz

- cp -rvf ~/vagrant/node_exporter/node_exporter-0.15.1.linux-amd64/* ~/Prometheus/node_exporter/


CREATING SERVICE FOR NODE-EXPORTER

- sudo vi /etc/systemd/system/node_exporter.service

ADD :-
 
[Unit]
Description=Node Exporter

[Service]
User=vagrant
ExecStart=/home/vagrant/Prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target


- sudo systemctl daemon-reload

- sudo systemctl enable node_exporter.service

- sudo systemctl start node_exporter.service


```
#######Creating Nginx Exporter#######
```

DOWNLOAD A BINARY

- wget https://github.com/hnlq715/nginx-vts-exporter/releases/download/v0.10.3/nginx-vts-exporter-0.10.3.linux-amd64.tar.gz

INSTALLING

- tar -xzzvf nginx-vts-exporter-0.10.3.linux-amd64.tar.gz



CREATING SERVICE FOR NGINX EXPORTER

- sudo vi /etc/systemd/system/nginx_exporter.service

ADD :-

[Unit]
Description=Nginx Exporter

[Service]
User=vagrant
ExecStart=/home/vagrant/nginx-vts-exporter-0.10.3.linux-amd64/nginx-vts-exporter

[Install]
WantedBy=default.target


- sudo systemctl daemon-reload

- sudo systemctl enable node_exporter.service

- sudo systemctl start node_exporter.service


```
#######Creating Jenkins Exporter#######
```

DOWNLOAD A BINARY

- wget https://github.com/lovoo/jenkins_exporter/archive/1.1.0.tar.gz




INSTALLING

- tar -xzvf 1.1.0.tar.gz

- pip install requests

-pip install prometheus-client

CREATING SERVICE FOR JENKINS EXPORTER

- sudo vi /etc/systemd/system/jenkins_exporter.service

ADD :-

[Unit]
Description=jenkins_Exporter

[Service]
Environment=jenkins='http://localhost:8080/'
User=vagrant
ExecStart=/home/vagrant/jenkins_exporter-1.1.0/jenkins_exporter.py -j $jenkins
Restart=on-failure

[Install]
WantedBy=multi-user.target


- sudo systemctl daemon-reload

- sudo systemctl enable jenkins_exporter.service

- sudo systemctl start jenkins_exporter.service


```
#######INSTALLING COLLECTD AND CONFIGURING IT WITH NGINX#######
```

- sudo apt-get install nginx
- sudo apt-get install collectd
- sudo service –status-all | egrep “collectd|nginx”
- cd /usr/share/doc/collectd/examples/
- sudo cp -r ./collection3 /var/www/html
- sudo apt-get install librrds-perl libconfig-general-perl libhtml-parser-perl  libregexp-common-perl
- sudo apt-get install spawn-fcgi fcgiwrap
- cd /etc/nginx/sites-enabled/
- sudo vi default

ADD:-

server {
    location /nginx_status {
        stub_status on;

        access_log off;
#        allow 127.0.0.1;
        allow all;

}

- service nginx reload
- sudo systemctl restart nginx


```
#######CREATING COLLECTD EXPORTER#######
```

DOWNLOAD A BINARY

- wget https://github.com/prometheus/collectd_exporter/releases/download/v0.4.0/collectd_exporter-0.4.0.linux-amd64.tar.gz


INSTALLING

- tar -xzvf collectd_exporter-0.4.0.linux-amd64.tar.gz

CREATING SERVICE FOR COLLECTD EXPORTER

- sudo vi /etc/systemd/system/collectd_exporter

ADD:-

[Unit]
Description=jenkins_Exporter

[Service]
User=vagrant
ExecStart=/home/vagrant/collectd_exporter-0.4.0.linux-amd64/collectd_exporter
Restart=on-failure

[Install]
WantedBy=multi-user.target

- sudo systemctl daemon-reload

- sudo systemctl enable jenkins_exporter.service

- sudo systemctl start jenkins_exporter.service




![promethus1](https://github.com/lovedeepsh/Monitoring/blob/master/Monitoring_day1_images/prometheus1.png)
![prometheus2](https://github.com/lovedeepsh/Monitoring/blob/master/Monitoring_day1_images/prometheus2.png)

