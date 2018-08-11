- Install [guide](https://www.digitalocean.com/community/tutorials/how-to-create-a-point-to-point-vpn-with-wireguard-on-ubuntu-16-04)
```
$ sudo add-apt-repository ppa:wireguard/wireguard
$ sudo apt-get update
$ sudo apt-get install wireguard-dkms wireguard-tools
```

- create private key
```
# create config file
$ (umask 077 && printf "[Interface]\nPrivateKey = " | sudo tee /etc/wireguard/wg0.conf > /dev/null)
# generate public & private key and write private key to config file
$ wg genkey | sudo tee -a /etc/wireguard/wg0.conf | wg pubkey | sudo tee /etc/wireguard/publickey
```

- edit config file`/etc/wireguard/wg0.conf` & write
```
[Interface]
PrivateKey = generated_private_key
ListenPort = 5555
SaveConfig = true
Address = 10.0.0.1/24
```
