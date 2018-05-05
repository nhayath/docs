#### Install
- install latest version

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt update
$ sudo apt-get install -y docker-ce
$ sudo service docker status

```
- add user to docker group
  
```
$ sudo usermod -aG docker ${USER}
$ su - ${USER}
# check if add to docker group
$ id -nG
```

- search for docker images
```
$ doker search <image>
```
- to download image
```
$ docker pull <image>
```
- to run the downloaded container
```
$ docker run <container>
# interactive run
$ docker run -it <container>
```
- to check downloaded images
```
$ docker images
```
- get list of docker containers
```
$ docker ps
# all active or inactive containers
$ docker ps -a
```
- To view the latest container you created
```
$ docker ps -l
```
- Stopping a running or active container
```
$ docker stop <container-id>
```
- login in docker hub & push your image
```
$ docker login -u docker-registry-username
$ docker push docker-registry-username/docker-image-name
```
