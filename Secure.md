## Security tips to locking down your box
###### 1. SSH to your VPS (virtual private server)
###### 2. Change root password from default
```shell
passwd root
```
###### 3. Update VPS
```shell
sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade
```
###### 4. Create a user with restricted rights
```shell
## In Ubuntu
adduser username-with-restricted-rights
adduser username-with-restricted-rights sudo
## In CentOS
useradd username-with-restricted-rights && passwd username-with-restricted-rights
usermod -aG wheel username-with-restricted-rights


mkdir -p /home/username-with-restricted-rights/.ssh
nano /home/username-with-restricted-rights/.ssh/authorized_keys
sudo chmod -R 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys

```
###### 5. Change default SSH port and disable root login
```shell
## Lockdown - changing default ports
sudo nano /etc/ssh/sshd_config
## Disable root login
PermitRootLogin no
## Disable password based authentication. Highly recommended.Use public key based authentication
## Note - setup authorized_keys before enabling this option. You can get locked out of your VPS.
PasswordAuthentication no
## Change default SSH port to any random port (eg. 2230)
## Note - Now, when you request an SSH connection on your machine, 
## you will have to indicate the new port:
## ssh root@ip/hostname -p NewPort
Port 2230
# To listen only on IPv4 (Use this or IPv6)
AddressFamily inet
# To listen only on IPv6 (Use this or IPv4)
AddressFamily inet6

## Lockdown - Firewall 
sudo ufw limit ssh/tcp
sudo ufw allow 2230/tcp
sudo systemctl reload sshd
```
###### 6. Enable firewall
```shell
# Allow Incoming SSH from Specific IP Address or Subnet
sudo ufw allow from 192.168.0.0/16  to any port 2230
sudo ufw limit 2230/tcp comment 'SSH port'
sudo ufw enable && sudo ufw status
```
###### 7. Install fail2ban
```shell
sudo apt-get install fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
sudo service fail2ban restart
# Verify with
sudo iptables -L
```

###### 8. Disable IPv6, if you do not intend to use it
```shell
sudo nano /etc/sysconfig/network
# Set following values - 
NETWORKING_IPV6=no
IPV6INIT=no
```

###### 9. Determine Running Services
```shell
sudo ss -atpu
```
