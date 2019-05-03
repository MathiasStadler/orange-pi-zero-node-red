# node red on ornage pi zero

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
