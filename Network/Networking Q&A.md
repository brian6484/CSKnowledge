## How to update remote machine and troubleshoot issues
Updates are normally handled using tools like Ansible, Puppet, Chef or thru native package managers and are fetched from
repo with HTTPS. 

If some machines fail to update, i will check for issues like firewall rules blocking traffic, DNS resolution prob
(remote machine cant trnslate domain name to public IP address), unavailiability of package repo or misconfig in network segmentation.

## TLS 1.2 vs TLS 1.3 (NOT TCP!, TLS!)
TLS 1.2 requires multiple handshake round trips, which increase connection latency. It supports a wide range of cipher suites like the old legacy ciphers, which may not enforce secrecy by default

TLS 1.3 does the handshake in 1 RTT, reducing latency. It also allows strong, modern cipher suites with AEAD (Authenticated
Encryption with Associated Data) and enforces secrecy.
