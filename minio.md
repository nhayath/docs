- install using docker

```
$ docker run -p 9000:9000 --name minio1 -v /mnt/data:/data -v /mnt/config:/root/.minio minio/minio server /data
```
- copy public & secret key
- login using keys at `http://localhost:9000`
- to stop

```
$ docker stop minio1
```
- to start
```
$ docker start minio1
```