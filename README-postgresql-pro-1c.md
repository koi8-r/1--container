```
apt-get update -y
apt-get install -y wget gnupg2
wget -O - http://1c.postgrespro.ru/keys/GPG-KEY-POSTGRESPRO-1C | apt-key add -
echo deb http://1c.postgrespro.ru/archive/2017_12_26/deb stretch main > /etc/apt/sources.list.d/postgrespro-1c.list
apt-get update -y
apt-get install -y postgresql-pro-1c-9.6
```
