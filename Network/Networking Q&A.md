## How to update remote machine and troubleshoot issues
Updates are normally handled using tools like Ansible, Puppet, Chef or thru native package managers and are fetched from
repo with HTTPS. 

If some machines fail to update, i will check for issues like firewall rules blocking traffic, DNS resolution prob
(remote machine cant trnslate domain name to public IP address), unavailiability of package repo or misconfig in network segmentation.
