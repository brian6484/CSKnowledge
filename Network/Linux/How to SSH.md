## Values
we need 1) ip address 2) username 3) either the password or the pem/ppk file to that server 4) port.

- IP address or hostname (e.g., 192.168.1.100 or prod-server-01.company.com)
- Username (e.g., root, admin, or your company username)
- Authentication method (password, SSH key file, or told to use your existing keys)
- Maybe a port number if non-standard (default SSH is 22)

## command 
if the prod key is in ur home directory like /home/you/.ssh/prod_key. Then we use ~, which is a shortcut to ur home directory.

~ (Tilde): This is a shortcut that represents the current user's home directory.

For example, if your username is jdoe, the path ~/Documents is a shortcut for /home/jdoe/Documents.

-i = identity file (ssh file)
```
$ ssh -i ~/.ssh/prod_key sysadmin@10.50.2.15

## or if u using password
$ ssh sysadmin@10.50.2.15
## then will promt u for the password
```

