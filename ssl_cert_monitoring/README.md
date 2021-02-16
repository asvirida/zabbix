# Install

1. Copy `ssl_cert_check.sh` to `/etc/zabbix/scripts`
2. `chown -R zabbix. /etc/zabbix/scripts`
3. `chmod 0740 ssl_cert_check.sh`
4. Add to `/etc/zabbix/zabbix_agentd.conf`:

```
## Check SSL Certificate Expire
UserParameter=ssl_cert_check_valid[*], /etc/zabbix/scripts/ssl_cert_check.sh valid "$1" "$2" "$3" "$4"
UserParameter=ssl_cert_check_expire[*], /etc/zabbix/scripts/ssl_cert_check.sh expire "$1" "$2" "$3" "$4"
```


### Examples test:


```
user@host:~$ ./ssl_cert_check.sh valid valid.example.com
1

user@host:~$ ./ssl_cert_check.sh valid imap.valid.example.com 993
1

user@host:~$ ./ssl_cert_check.sh valid invalid.example.com
0

# Expired certificate is not valid
user@host:~$ ./ssl_cert_check.sh valid expired.example.com
0

user@host:~$ ./ssl_cert_check.sh expire effective-next-90-days.example.com
90

user@host:~$ ./ssl_cert_check.sh expire expired-37-days-ago.example.com
-37

# NOTE: an error message is shown to stderr only when running on a terminal
# Without terminal(from zabbix), only the result is printed to stdout
user@host:~$ ./ssl_cert_check.sh expire unavailable.example.com
-65535
ERROR: Failed to get certificate

# Check 127.0.0.1:443 for a valid certificate for example.com
# TLS SNI(Server Name Indication) is set to example.com
user@host:~$ ./ssl_cert_check.sh valid 127.0.0.1 443 example.com
1

# Check 127.0.0.1:443 for a valid certificate for example.com
# TLS SNI(Server Name Indication) is set to example.com
# Check timeout is 10 seconds(default is 5)
user@host:~$ ./ssl_cert_check.sh valid 127.0.0.1 443 example.com 10
```

### Test case via agent:
`zabbix_agentd -t ssl_cert_check_expire[example.com]`  

### Add item to host

Name: `Certificate example.com expire`  
Key: `ssl_cert_check_expire[example.com]`  
Update interval: `5m`  

### Add trigger to host

Name: `Certificate example.com expire less then 10 days`  
Expression: `{example.com:ssl_cert_check_expire[example.com].last()}<10`



