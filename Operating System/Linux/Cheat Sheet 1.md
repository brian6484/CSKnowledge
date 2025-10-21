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

### if theres bastion
```
ssh admin@bastion.company.name
ssh ubuntu@10.0.1.50
```
