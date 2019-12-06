## Frequently used Unix commands

#### Show IP Address
```ifconfig```

#### Test connectivity
ignore cert, verbose
```
curl -kv sqs.us-east-2.amazonaws.com
curl -kv s3.us-east-1.amazonaws.com
```

#### Show hops to destination
```
traceroute kms.us-east-1.amazonaws.com
```
Note: traceroute will show if destination is blocked. Also will show whether proxy is being used to go to the destination.

#### limit number of pings to 2
ping -c 2 google.com

#### Name servers 
/etc/resolv.conf

#### Name server used
```dig <domainname such as vpce-080d87fd98886ef41-520gvi3i.kms.us-east-1.vpce.amazonaws.com>```
will give details of which nameserver was used to resolve that domain name, and the result ip address found.
Same as nslookup.

#### configure firewalls
iptables 
iptables and other services in the linux kernel are supported by sysctl and /etc/sysctl.conf

#### open ports 
netstat -ntlp 

#### verify which process is listening at port 80
sudo netstat -tulpn | grep :80

SSH configuration 
/etc/ssh/sshd_config

Sudoers configuration
sssd service governs /etc/sudoers file

List of cron jobs
/etc/crontab 

#### list processes
```
top
ps -aux
```

#### List users
```
cat /etc/passwd 
```

#### Downloading files
wget 

#### Hosts file
/etc/hosts

#### To show the network interfaces
ip addr show

#### show all environment variables
export
env

#### To show proxy settings.
 env | grep -i proxy

#### To unset environment variables
unset http_proxy

#### find files
find <path> -name 'regularexpression'

#### Parsing text from Log file using regular expressions
Grep in its common usage prints out the entire matching line. But if you are looking to output just the matched content from each line, use grep -o option.

Usage
```
grep -o '<regex>' file 
```
For example
```
echo 'Hello World 123' | grep -o '[0-9]*'
```
