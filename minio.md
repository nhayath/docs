- [doc](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-object-storage-server-using-minio-on-ubuntu-16-04)
- download minio
```
$ wget https://dl.minio.io/server/minio/release/linux-amd64/minio
$ sudo chmod +x minio
# move minio folder to `/usr/local/bin`
$ sudo mv minio /usr/local/bin
# add new user `minio`
$ sudo useradd -r minio -s /sbin/nologin
# make user `minio` owner of `/usr/local/bin/minio` folder
$ sudo chown minio:minio /usr/local/bin/minio
# create directory where minio will store files
$ mkdir /data/minio
# give user `minio` ownership to `/data/minio`
$ sudo chown minio:minio /data/mino
# create config dir for minio and change ownership to minio
$ sudo mkdir /etc/minio
$ sudo chown minio:minio /etc/minio
```
- create default config file
```
$ sudo nano /etc/default/mino
```
- and put 
```
MINIO_VOLUMES="/data/minio/"
MINIO_OPTS="-C /etc/minio --address :9000"
```
- download startup scripts
- change user name to `minio` in startup scripts
- follow the doc for rest
- access & secret key is in `/etc/minio/config.json`
- install client
```
$ wget https://dl.minio.io/client/mc/release/linux-amd64/mc
$ chmod +x mc
$ ./mc --help
```
- create alias with access & secret key
```
$ ./mc config host add <alias> <minio-end-point> <access-key> <secret-key>
# get list of all buckets
$ ./mc ls <alias>
```

### SSL
- install certbot
```
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get install python-certbot-nginx
```

- edit `/etc/nginx/sites-available/default` & replace
```
server {
 listen 80;
 server_name cdn.example.com;
 location / {
   proxy_set_header Host $http_host;
   proxy_pass http://localhost:9000;
   #health_check uri=/minio/health/ready;
 }
}
```
- update firewall
```
$ sudo ufw allow 'Nginx Full'
$ sudo ufw delete allow 'Nginx HTTP'
```
- install certificate
```
$ sudo certbot --nginx -d cdn.example.com
```
- commans
```
# list buckets
$ ./mc ls <alias>
# create bucket
```

#### Rclone
- install
```
$ sudo apt update
$ curl https://rclone.org/install.sh | sudo bash
$ mkdir -p ~/.config/rclone
$ nano ~/.config/rclone/rclone.conf

```
- edit file `~/.config/rclone/rclone.conf` and put
```
[spaces]
type = s3
env_auth = false
access_key_id = spaces_access_key
secret_access_key = spaces_secret_key
endpoint = ams3.digitaloceanspaces.com
acl = private

[minio]
type = s3
env_auth = false
access_key_id = minio_access_key
secret_access_key = minio_secret_key
region = uk-east-1
endpoint = cdn.example.co.uk
location_constraint =
server_side_encryption =
```
- change conf dir permission to secure it
```
$ chmod 600 ~/.config/rclone/rclone.conf
```
- check if working
```
# list all remote locations
$ rclone listremotes
# list bucket for spaces
$ rclone lsd spaces:
# list buckets for minio
$ rclone lsd minio:
```
- sync from spaces to minio
```
$ rclone sync spaces:bucket minio:bucket
```