---
title: Accessing Database Backups
layout: default
parent: Wiki Imports
---


## Current - AWS Backups

Backups are stored in S3 as versions of the `cubing.sql` file. 

QUICK LINK: https://s3.console.aws.amazon.com/s3/object/wca-backups?region=us-west-2&prefix=latest%2Fwca_db%2Fcubing.sql&tab=versions
1. Navigate to AWS console
2. S3 -> Buckets -> wca-backups -> latest
3. Toggle "Show versions" to see a version history, and download the one you need
4. Bear in mind that you can just grep this file for basic data - you don't always need to load it into a SQL client


## Deprecated - tarsnap backups:

We backup the production database to tarsnap weekly.

The easiest way to get to them is by sshing to the production server and running:
```bash
tarsnap --list-archives | sort
```

>...  
wca-backup-20170529_000922  
wca-backup-20170605_001107  
wca-backup-20170612_000909  
wca-backup-20170619_000838  
wca-backup-20170626_000934  
wca-backup-20170703_001047  
wca-backup-20170710_001104  
wca-backup-20170717_001036  
wca-backup-20170724_001003  
wca-backup-20170731_000945


To get the contents of the database from June 12th, you can run the following command:

```bash
tarsnap -Oxf wca-backup-20170612_000909 secrets/wca_db/cubing.sql > /tmp/cubing.sql
```

Now that you have this file the safest is probably to use it locally (do not import it onto staging, or else the whole world will be able to access it via phpMyAdmin on staging!).

Before moving the file to another machine it's best to tar it so that it shrinks to a reasonable size:
```bash
/tmp @production> cd /tmp
/tmp @production> tar -czvf cubing.sql.tar.gz cubing.sql
```

Over on your laptop now, you can scp, extract the file and import it into a newly created database:

```bash
/tmp @yourlaptop> scp worldcubeassociation.org:/tmp/cubing.sql.tar.gz .
cubing.sql.tar.gz                                                       100%  140MB  28.0MB/s   00:05
/tmp @yourlaptop> tar xvf cubing.sql.tar.gz
cubing.sql
/tmp @yourlaptop> mysql
mysql> create database wca_backup_20170612_000909;
Query OK, 1 row affected (0.01 sec)

mysql> use wca_backup_20170612_000909;
Database changed
mysql> source cubing.sql;
```
