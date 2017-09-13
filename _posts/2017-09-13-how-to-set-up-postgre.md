---
layout: post
title:  "How to set up postgre database from scratch"
date:   2017-09-13
excerpt: "working in musical.ly"
tag:
- Linux
- Postgre Sql
---


# How to set up postgre sql database on Amazon EC2

1. download and install postgre
```
sudo su -
apt-get install postgresql postgresql-contrib
```

2. config postgre server and start
```
update-rc.d postgresql enable
service postgresql start
```

3. connect to postgresql with default user postgre
```
sudo su - postgres
psql -U postgres
```
4. create new root user with superuser role
```
CREATE USER root SUPERUSER;
ALTER USER root WITH PASSWORD '$password';
```
5. login with new user and create new database
```
sudo psql -U root -d postgres
create database $databsename
```
6. import db file from other database
```
sudo pg_dump -U root dbname > /data_lvm/dbname_file
sudo psql  -U root dbname < infile
```
