####安装:deb for Debian/Ubuntu, rpm for Redhat/Centos/Fedora, mac for OS X
```
deb:
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-5.1.1-amd64.deb
sudo dpkg -i filebeat-5.1.1-amd64.deb
rpm:
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-5.1.1-x86_64.rpm
sudo rpm -vi filebeat-5.1.1-x86_64.rpm
mac:
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-5.1.1-darwin-x86_64.tar.gz
tar xzvf filebeat-5.1.1-darwin-x86_64.tar.gz
```