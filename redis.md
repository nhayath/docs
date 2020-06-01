## Backup & Restore
- backup file
```
/var/lib/redis/dump.rdb
```
- backup from `redis-cli`
```
redis-cli > save
```
- take snapshot every 15 minutes
```
save 900 1
```

- backup from terminal
```
sudo cp /var/lib/redis/dump.rdb /home/sammy/redis-backup-001
```

## Configuration
- set maximum memory to 256mb
```
> config set maxmemory '256mb'
```
- check current maxmemory
```
> config get maxmemory
```
- set maxmemory-policy to delete less frequent keys if memory limit reached
```
> config set maxmemory-policy 'allkeys-lru'
```
- get maxmemory-policy
```
> config get maxmemory-policy
```

* note: this is not permanent, will be reset on restart, to make permanent change the config file

## Useful commands

- delete every records
```
> flushall
```

## Restore
- stop redis-server
- check `/etc/redis/redis.conf` file and set appendonly to `no` if it's not already
- copy dump file to /var/lib/redis
- change file user and group to redis:redis
- change permission to 644
- start server
