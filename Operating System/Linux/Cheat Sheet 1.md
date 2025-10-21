## Scenarios
### cant even ssh into server 
u can look [here](https://github.com/brian6484/CSKnowledge/blob/main/Network/Linux/How%20to%20SSH.md)

```
ssh -i ~/.ssh/ssh_key brian@7.7.7.7

## or with password if it doesnt use key
ssh brian@7.7.7.7
## then it will promt for password input
```

to find ssh private key the common location is
```
ls -la ~/.ssh/
```

to find the config and username
```
## check ssh config 
cat ~/.ssh/config
Host myserver
    HostName 192.168.1.100
    User admin
    IdentityFile ~/.ssh/id_rsa
    Port 22

## check bash history for prev ssh commands
history | grep ssh
```

### ssh, curl, telnet all fail
then check network/routing cuz there might been network issue between me and the server(10.50.2.15). Or could be firewall or that server might be down.

we can check via ip route, which shows routing table and where does network traffic go
```bash
ip route

default via 192.168.1.1 dev wlp3s0 proto dhcp metric 600 
192.168.1.0/24 dev wlp3s0 proto kernel scope link src 192.168.1.45 metric 600
```
This shows i am on 192/24 network but that server is in a completely diff subnet

lets check if we have vpn connection via ip link show, which shows network interfaces. 
```
$ ip link show
```

**Result:**
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DORMANT group default qlen 1000
    link/ether a4:34:d9:8f:12:c3 brd ff:ff:ff:ff:ff:ff
```
theres no vpn interface like tun0 or wg0

check firewall rule
```
sudo iptables -L -n

Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

### if theres bastion
```
ssh admin@bastion.company.name
ssh ubuntu@10.0.1.50
```
