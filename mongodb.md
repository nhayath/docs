#### Install
- add latest `repo` from the link below:
- https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/
- then
```
$ suod apt install -y mongodb-org
```

#### Secure
- https://medium.com/@matteocontrini/how-to-setup-auth-in-mongodb-3-0-properly-86b60aeef7e8
- create admin
```
> use admin
> db.createUser({ user: "admin", pwd: "adminpassword", roles: [{ role: "userAdminAnyDatabase", db: "admin" }] })

# check if working
> db.auth("admin", "adminpassword")

# exit the shell
> exit

```

- edit `/etc/mongod.conf`
- uncomment `#security`
- and at the line below add with a `SPACE` `authorization: "enabled"`
```
security:
 authorization: enabled
```
- restart mongodb
- connect to shell again
```
mongo -u admin -p --authenticationDatabase admin
```
- create database & it's user
```
# create database
# login as admin
> use admin
> db.auth("admin", "pass")
> use <dbname>
# create `db-user`
> db.createUser({ user: "youruser", pwd: "yourpassword", roles: [{ role: "dbOwner", db: "yourdatabase" }] })
# switch to new user
> db.auth('newuser', 'pass')
> show collections
```
- allow connection from `localhost` only
- edit `/etc/mongod.conf`
- & set bind ip to `127.0.0.1`

#### Remote Connection
- To allow a client to connect remotely
- edit `/etc/mongod.conf` & bind mongodb to server's public address
- set `bindIp=127.0.0.1,server_public_ip_address,...`
- Set firewall

```
$ sudo ufw allow from <client_ip_address> to any port 27017
$ sudo ufw status
```
### Bulk update using robomongo
```
db.getCollection('categories').update({}, {$set: {isActive: NumberInt(1)}}, {upsert: true, multi: true})
```

#### Remove by date
```
db.getCollection('products').remove({'store._id': 10, 'category._id': 12, createdAt: { $gte : ISODate("2018-06-21T21:00:00Z") } })
```

#### Backup & Restore
- Backup
```
# uncompress
$ mongodump --db <db-name> --username <user> --password <pass>

# compress
mongodump --db <db-name> --username <user> --password <pass> --gzip --archive=/home/nasim/Downloads/mongo-cs.gz

# for remote db
$ mongodump --host <host_ip_or_address> --db <db-name> --username <user> --password <pass> --gzip --archive=/home/nasim/Downloads/mongo-cs.gz

```
- Restore
```
# all db
mongorestore --gzip --archive=/path/to/archive
# specific db with security enabled
> mongorestore --db <db-name> --username <user> --password <pass> --gzip --archive=tmp/dump.gz
# user --drop flah if you want to completely overwrite
```

- Monitor
- login as admin
```
# server status
db.serverStatus()
# check number of connection
db.serverStatus().connections
```