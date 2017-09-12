### TODO:
- How to create telegram bot for notifications
- How to get the token

##
https://github.com/firehol/netdata/issues/826
https://github.com/firehol/netdata/wiki/telegram-notifications

## Notification on IP change
Took inspiration from:
https://ariandy1.wordpress.com/2014/04/08/linux-send-email-when-ip-address-changes/
http://metz.gehn.net/2016/01/monitoring-notifications-via-telegram/

IP change script, get it running as root `sudo su` and create file with `vi /root/ip_change_telegram.sh`
```
#!/bin/bash
# check and send ip address to telegram

MYIP=`ifconfig eth0 | grep 'inet addr'| awk '{print $2}' | cut -d ':' -f 2`;
MYWLANIP=`ifconfig wlan0 | grep 'inet addr'| awk '{print $2}' | cut -d ':' -f 2`;
TIME=`date`;
TOKEN=YOUR_TOKEN_GOES_RIGHT_HERE
CHAT_ID=YOUR_CHAT_ID_GOES_RIGHT_HERE

LASTIPFILE='/root/.last_ip_addr';
LASTIP=`cat ${LASTIPFILE}`;

if [ ${MYIP} != ${LASTIP} ] || [${MYWLANIP} != ${LASTIP}]
then
        echo "New IP = ${MYIP} or ${MYWLANIP}"
        curl --silent \
          --data-urlencode "chat_id=${CHAT_ID}" \
          --data-urlencode "text=${MYIP} ${MYWLANIP}" \
          "https://api.telegram.org/bot${TOKEN}/sendMessage"
        if [ ! -z ${MYWLANIP} ]; then echo ${MYWLANIP} > ${LASTIPFILE}; fi
        if [ ! -z ${MYIP} ]; then echo ${MYIP} > ${LASTIPFILE}; fi
else
        echo "no IP change!"
fi
```
Also set cron to regularly run the script.
```
echo "*/1 * * * * root /bin/bash /root/ip_change_telegram.sh" >> /etc/crontab
```
Check root's cron with `sudo crontab -l` or `less /var/spool/cron/crontabs/root`
