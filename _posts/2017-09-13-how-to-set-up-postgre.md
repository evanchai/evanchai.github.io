---
layout: post
title:  "How to set up postgre database from scratch"
date:   2017-09-13
excerpt: "working in musical.ly"
tag:
- Linux
- Postgre Sql
---


## How to set up postgre sql database on Amazon EC2

0.Prepare apt env on EC2
~~~~
sudo add-apt-repository "deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main"

wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

sudo apt-get update
~~~~

1.check if there is postgres running in your ec2,if there,stop and uninstall them

* check posgtre process
~~~~
ps -ef | grep postgre
~~~~

* stop postgre server
~~~~
sudo service postgresql stop
~~~~
terminal return: * Stopping PostgreSQL 9.3 database server

* remove specific postgre version from apt-get
~~~~
sudo apt-get autoremove postgresql-9.3
~~~~

2.download and install postgre

~~~~
sudo apt-get install postgresql-9.4
~~~~

3.connect to postgresql with default user postgre
```
sudo su - postgres
psql -U postgres
```

4.create new root user with superuser role

```
CREATE USER root SUPERUSER;
ALTER USER root WITH PASSWORD '$password';
```

5.login with new user and create new database

```
sudo psql -U root -d postgres
create database $databsename
```

6.(**optional**)change postgre data-dir to /data_lvm, in case of disk resource limitation 

* stop postgre server first
~~~~
sudo service postgresql stop
~~~~

* make new data dir and assign roles
 ~~~~
 sudo mkdir -p /data_lvm/postgres/9.4/main
 sudo chown -R postgres:postgres /data_lvm/postgres
 sudo chmod 700 /data_lvm/postgres/9.4/main
 ~~~~

* move old postgre data dir to new destination
~~~~
sudo su
mv /var/lib/postgresql/9.4/main/* /data_lvm/postgres/9.4/main/
~~~~

* change postgre default.conf for data dir,update line below in /etc/postgresql/9.4/main/postgresql.conf
~~~~ 
data_directory = '/data_lvm/postgres/9.4/main'
~~~~

* start postgre server
 ~~~~
 sudo service postgresql start
 ~~~~

7.configuration for pg_hba.conf & posgtresql.conf

* put next lines at the end of file @ /etc/postgresql/9.4/main/pg_hba.conf
~~~~
host  all  root 0.0.0.0/0 md5
host  all  ambari 0.0.0.0/0 md5
host  all  hive 0.0.0.0/0 md5
host  all  oozie 0.0.0.0/0 md5
~~~~  

* change ip listening from localhost to all ips @ /etc/postgresql/9.4/main/posgtresql.conf
~~~~
listen_addresses = '*' 
~~~~

* change listening port to 5432 @ /etc/postgresql/9.4/main/posgtresql.conf
~~~~
port = 5432
~~~~
 


8.import db file from other database

```
sudo pg_dump -Fc -b -d dbname -f /data_lvm/dbname_file.gz
sudo pg_restore  -d dbname /data_lvm/dbname_file.gz
```


