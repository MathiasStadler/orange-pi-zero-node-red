# node red on orange pi zero

## install

- follow this site

```txt
https://diyprojects.io/install-node-red-orange-pi-running-armbian/
```

```bash
# bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)
# I don't like direct running script
# so i make a triple step
curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered -o install-node-red-on-opi.sh
chmod +x install-node-red-on-opi.sh
./install-node-red-on-opi.sh
# follow the step in the output
```

## pm2

- PM2, a process manager for Node.js
- start/stop on start/stop/reboot and continue control process control

```bash
#install
sudo npm install -g pm2

# where is node-red installed
which node-red
> /usr/bin/node-red

# start node-red via pm2
pm2 start /usr/bin/node-red -- -v

# save config, follow the instruction
pm2 save

# start pm2 with config ; save in ~/.pm2  follow the instruction
pm2 startup

# status of running process
pm2 status

# stop node-red
pm2 stop node-red

# strat node-red
pm2 start node-red
```

## enable GPIO ports for node-red on opi zero

- sources

```txt
https://diyprojects.io/orange-pi-review-opi-gpio-package-node-red-node-red-contrib-opi-gpio/
https://flows.nodered.org/node/node-red-contrib-opi-gpio
```

- install npm packages

```bash
npm install node-red-contrib-opi-gpio
```

- add group

```bash
sudo addgroup gpio
```

- add group to your $USER

```bash
sudo usermod -a -G gpio $USER
```

- check the your $USER is member of group gpio

```bash
groups $USER
```

- add script for change owner by generated new gpio files and directory

```bash
cat << EOF |sudo tee /etc/udev/rules.d/99-com.rules
KERNEL=="gpio*", RUN="/bin/sh -c 'chgrp -R gpio /sys/%p /sys/class/gpio && chmod -R g+w /sys/%p /sys/class/gpio'"
EOF
```

- restart your system

```bash
sudo shutdown -Fr now
```

## space

```txt
https://groups.google.com/forum/#!topic/node-red/7dlIfciW3dI

```

## docker

- source

```txt
# docker image
https://nodered.org/docs/getting-started/docker

# alexa amazon device adapter
https://flows.nodered.org/node/node-red-contrib-amazon-echo
```

```bash create_volume_amazon-node-red.sh

# volume for flows should be mounted under /data
docker volume create volume-amazon-node-red
```

```bash install_container.sh
CURRENT_CID_ID="/tmp/amazon-node-red-current_cid.id"
# port 8090 for node-red  install node-red-contrib-amazon-echo change settings in ui
# port 1880 for node-red ui
export CURRENT_CID=$(docker run -it --rm  -d -v source=volume-amazon-node-red,target=/data  -p 8090:8090 -p 1880:1880 --name amazon-node-red nodered/node-red)
echo $CURRENT_CID > "${CURRENT_CID_ID}"
```

```bash enable-log.sh
docker logs -f "$(cat ${CURRENT_CID_ID})"
echo "$(cat ${CURRENT_CID_ID})"
```

```bash install node-red flow
docker exec  "$(cat ${CURRENT_CID_ID})" /bin/sh -c "npm install node-red-contrib-amazon-echo"
```

```bash restart_docker.sh
CURRENT_CID_ID="/tmp/amazon-node-red-current_cid.id"
# port 8090 for node-red  install node-red-contrib-amazon-echo change settings in ui
# port 1880 for node-red ui
export CURRENT_CID=$(docker run -it --rm  -d -v source=volume-amazon-node-red,target=/data  -p 8090:8090 -p 1880:1880 --name amazon-node-red nodered/node-red)
echo $CURRENT_CID > "${CURRENT_CID_ID}"
```


```bash
sudo apt-get install iptables-persistent
sudo iptables -I INPUT 1 -p tcp --dport 80 -j ACCEPT
sudo iptables -A PREROUTING -t nat -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080
sudo iptables -t nat -L
sudo sh -c "iptables-save > /etc/iptables/rules.v4"
```
