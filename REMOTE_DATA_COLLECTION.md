## Remote Data Collection 

Using Splunk Universal Forwarder. We are able to collect and sync remote collection, such as HTTP logs,Database logs etc.

### Installation
1. Install Splunk Universal Forwarder
Debian/Ubuntu: 
```
$ wget -O splunkforwarder-8.0.6-152fb4b2bb96-linux-2.6-amd64.deb 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=8.0.6&product=universalforwarder&filename=splunkforwarder-8.0.6-152fb4b2bb96-linux-2.6-amd64.deb&wget=true’
```

Redhat/Centos:
```
$ wget -O splunkforwarder-8.0.6-152fb4b2bb96-linux-2.6-x86_64.rpm 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=8.0.6&product=universalforwarder&filename=splunkforwarder-8.0.6-152fb4b2bb96-linux-2.6-x86_64.rpm&wget=true'
```

2. Start up Splunk Universal Forwarder binary
```
$ /opt/splunkforwarder/bin/splunk start --accept-license
```

Verify the Splunk Universal Forwarder is running
```
$ ps aux | grep splunk
```

3. Configure Splunk Universal Forwarder to start automatically on reboot
```
$ /opt/splunkforwarder/bin/splunk enable boot-start
```

### Configurations

Scenario:  To collect Nginx HTTP Access & Error logs

1. Configure Nginx to generate logs in JSON format. 

Add the following to the yoursite.conf file
```
access_log /var/log/nginx/access.json.log json;
error_log /var/log/nginx/error.json.log json;
```

Add below config to nginx.conf
```
log_format json escape=json '{'
                              '"time_local": "$time_local",'
                              '"core": {'
                                '"body_bytes_sent": "$body_bytes_sent",'
                                '"remote_addr": "$remote_addr",'
                                '"remote_user": "$remote_user",'
                                '"request": "$request",'
                                '"http": {'
                                  '"http_referer": "$http_referer",'
                                  '"http_user_agent": "$http_user_agent",'
                                  '"http_x_forwarded_for": "$http_x_forwarded_for"'
                                '}'
                              '}'
                            '}';
```

2. Configure Univeral Forwarder to forward the logs over TCP to the Splunk indexer.

Edit outputs.conf file
On Linux
```
$ vim /opt/splunkforwarder/etc/system/local/outputs.conf
```
Add the following
```
[tcpout]
defaultGroup = nginx

[tcpout:nginx]
server = mysplunk.mysite.com:9997

[tcpout-server://mysplunk.mysite.com:9997]

```
Edit inputs.conf file

```
$ vim /opt/splunkforwarder/etc/system/local/inputs.conf
```

Add the following
```
# Monitor NGINX Logs

[monitor:///var/log/nginx/access.json.log]
disabled = false
sourcetype = nginx:yoursite:access_logs


[monitor:///var/log/nginx/error.json.log]
disabled = false
sourcetype = nginx:yoursite:error_logs
```

3. Restart Splunk Universal Forwarder
```

```
