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

#### Backup & Restore
- Backup
```
# uncompress
$ mongodump --db <db-name> --username <user> --password <pass>

# compress
mongodump --db <db-name> --username <user> --password <pass> --gzip --archive=/home/nasim/Downloads/mongo-cs.gz

```
- Restore
```
# all db
mongorestore --gzip --archive=/path/to/archive
# specific db with security enabled
> mongorestore --db <db-name> --username <user> --password <pass> --gzip --archive=tmp/dump.gz
```