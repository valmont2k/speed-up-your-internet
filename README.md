# speed-up-your-internet


We will set up local dns resolver with warming dns cache for speed up your internet

Debian 
1) install pdns-recursor and namebench
sudo apt install pdns-recursor namebench tar
edit your  /etc/powerdns/recursor.conf file and add line:
minimum-ttl-override=899

/etc/init.d/pdns-recursor restart

2) install dnsperf 
sudo apt-get install -y bind9utils libbind-dev libkrb5-dev libssl-dev libcap-dev libxml2-dev make
sudo apt-get install -y libbind-dev libkrb5-dev libssl-dev libcap-dev libxml2-dev libjson-c-dev libgeoip-dev
mkdir -p /usr/local/src
cd /usr/local/src
wget https://www.dns-oarc.net/files/dnsperf/dnsperf-2.3.2.tar.gz
tar -xzf dnsperf-2.3.2.tar.gz
cd ./dnsperf-2.3.2
./configure
make
make install

3) create warming script and configure run it
create file /usr/local/bin/warmupdns.sh
add lines


#!/bin/bash
cat /usr/share/namebench/data/alexa-top-2000-domains.txt | awk  '{print $2 " A" }' > /tmp/test.dns
nsperf -Q 200 -q 1 -t 3  -d /tmp/test.dns -s 127.0.0.1 >> /var/log/dnsperf.log

4) add in crontab every 10 minutes /usr/local/bin/warmupdns.sh

5) test your config 
namebench -x -O 127.0.0.1 8.8.4.4

right result - something like this 
https://chart.apis.google.com/chart?cht=lxy&chs=720x415&chxt=x,y&chg=10,20&chxr=0,0,200%7C1,0,100&chd=t:0,1,1,2,3,4,4,79,1750%7C0,0,19,47,82,92,96,100,100%7C0,62,64,65,67,68,70,71,74,77,78,82,85,88,92,97,98,102%7C0,0,5,10,14,18,22,26,30,34,38,42,46,49,53,58,62,66&chco=ff9900,1a00ff&chxt=x,y,x,y&chxl=2:%7C%7CDuration+in+ms%7C%7C3:%7C%7C%25%7C&chdl=127.0.0.1%7C8.8.4.4





