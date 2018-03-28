# MySql
- Administration
    - Create User 
    - Grant a User to Full Access to a Database
- Backup & Restore
    - All Database Backup & Restore
    - Single Database Backup & Restore
- Query

#### Administration

> Create Database

```
mysql > CREATE DATABASE database name;
```

> Drop Database
```
mysql > DROP DATABASE dbname;
```

> Create User

```
mysql > CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';
```

> Grant a User to Full Access to a Database

```
mysql > GRANT ALL PRIVILEGES ON dbname.* TO 'newuser'@'localhost';
mysql > FLUSH PRIVILEGES;
mysql > SHOW GRANTS username;
``` 

> Useful Commands

```
# show databases
mysql > show databases;

# show tables
mysql > show tables;

# view table schema
mysql > describe tablename;

# show all users
msyql > select user from msyql.users;

# change user password
SET PASSWORD FOR 'jeffrey'@'localhost' = PASSWORD('cleartext password');
FLUSH PRIVILEGES;
```

#### Backup & Restore

##### |- All Database Backup & Restore
- Backup & Compress all Databases
```
mysqldump -u dbuser -p --all-databases | gzip > dump.sql.gz
```
- Restore all Databases
```
gunzip < dump.sql.gz | mysql -u dbuser -p
```

##### |- Single Database Backup & Restore
- Backup a single database & compress
```
mysqldump -u root -p <database> | gzip -9 > <database>-dd.mm.yyyy.sql.gz
```
- Restore
```
gunzip < <database>.sql.gz | mysql -u <username> -p<password> <database>
```