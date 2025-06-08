---
title: "MMS with Matrix"
draft: false
menu:
  docs:
    title:
    parent: "PinePhone/Software_tricks"
    identifier: "PinePhone/Software_tricks/MMS_with_Matrix"
    weight:
aliases:
  - /wiki/PinePhone_MMS_with_Matrix
---

{{< figure src="/documentation/images/Pinephonematrixfractalmmsscreenshot.jpg" caption="Screenshot of fractal displaying an MMS conversation" >}}

The one known way to get MMS fully (meaning pictures and group messages) working is by relaying all SMS and MMS to a matrix server and using a matrix client to interact with them. https://gitlab.com/untidylamp/mmmpuppet is the bridge used here

Outgoing messages over the max size are sent as a link. The link will not resolve if matrix is hosted on the PinePhone itself, so resize your images. An easy way might be to take a screenshot of your image viewer and send that image instead.

This method works with a local-to-the-pinephone matrix server but you could instead use a public one on the internet. Free accounts on matrix.org should work fine for example. Sleep may need to be disabled for non-local servers or the bridge can get stuck.

## Install packages

### Arch
Start with a nice and up-to-date Danctnix' Arch ARM PinePhone installation, mine is from April 20 2021. SSH into the PinePhone and then run this to install all the needed packages

```
sudo pacman -Sy matrix-synapse fractal python-matrix_client python-gobject git meson ninja base-devel python-matrix-nio python-dbus
```

Start the service

    sudo systemctl enable synapse
    sudo systemctl start synapse

### Mobian

Flash a fresh mobian nightly (Tested September 28 2021) and install the following:

`sudo apt install matrix-synapse fractal mmsd-tng python3-matrix-nio python3-vobject python3-aiofiles git`

## Set up Matrix Synapse on localhost

Skip this if you will be using a remote homeserver. Make a new config with the server name set to local host.

### Arch

    cd /etc/synapse/
    sudo python -m synapse.app.homeserver --server-name localhost --config-path homeserver.yaml --generate-config --report-stats=no

    sudo vi /usr/lib/systemd/user/matrix-synapse.service

```
Description=Multimedia Messaging Service Daemon
After=ModemManager.service

ExecStart= python3 -m synapse.app.homeserver --config-path=/etc/matrix/homeserver.yaml

Restart=on-failure
RestartSec=10s

WantedBy=default.target
```

Start the service

    systemctl enable matrix-synapse --user
    systemctl start matrix-synapse --user

### Mobian

    cd /etc/matrix-synapse/
    sudo rm homeserver.*
    sudo python3 -m synapse.app.homeserver --server-name localhost --config-path homeserver.yaml --generate-config --report-stats=no
    sudo service matrix-synapse start

### Add new matrix users

in /etc/synapse/ (arch) or /etc/matrix-synapse/ (mobian)

    register_new_matrix_user -c homeserver.yaml http://localhost:8008 # New user name and pw will both be pp
    register_new_matrix_user -c homeserver.yaml http://localhost:8008 # New user name and pw will both be mm

Open fractal and log into the homeserver at http://localhost:8008 with username pp and password pp

## Set up MMSD

### From git

Note: historical, no longer needed, mmsdtng commonly packaged

Grab the git repository and install it:

```
cd ~
git clone https://source.puri.sm/kop316/mmsd.git
cd mmsd
meson _build
meson compile -C _build
meson test -C _build
sudo meson install -C _build
```

```
sudo vi /usr/lib/systemd/user/mmsd-mm.service

Description=Multimedia Messaging Service Daemon
After=ModemManager.service

ExecStart=/usr/local/bin/mmsd -n -d

Restart=on-failure
RestartSec=10s

WantedBy=default.target
```

```
sudo chmod 644 /usr/lib/systemd/user/mmsd-mm.service
systemctl enable mmsd-mm.service --user
systemctl start mmsd-mm --user
```

### Settings for T-Mobile

This config works for me

After starting mmsdtng the first time it should generate a config. Edit the following 3 options:
```
vi ~/.mms/modemmanager/ModemManagerSettings

CarrierMMSC=http://mms.msg.eng.t-mobile.com/mms/wapenc
MMS_APN=fast.t-mobile.com
AutoProcessSMSWAP=true
```

### Restart MMSD ModemManager service

    systemctl restart mmsdtng

## Install MMS bridge

Grab it from git and put things in places

```
cd ~
git clone https://gitlab.com/untidylamp/mmmpuppet.git
cd mmmpuppet
chmod +x mmmpuppet.py
sudo cp mmm*.py /usr/local/bin/
mkdir -p $HOME/.config/mmm/
cp conf.json.sample $HOME/.config/mmm/conf.json
```

### Configure MMS bridge

This will mostly take care of editing the config for you if you are running a local matrix server.

```
sed -i 's^"https://matrix-client.matrix.org"^"http://localhost:8008"^' $HOME/.config/mmm/conf.json
sed -i 's^"@bot_account:matrix.org"^"@mm:localhost"^' $HOME/.config/mmm/conf.json
sed -i 's^"Change_me"^"mm"^' $HOME/.config/mmm/conf.json
sed -i 's^"@your_accounts:matrix.org"^"@pp:localhost"^' $HOME/.config/mmm/conf.json
```

You actually have to fill these two out yourself. I put "US" and my +1 and rest of 10 digit number.

```
vi  $HOME/.config/mmm/conf.json

"cell_number":      "+15554441234",
"cell_country":     "CA",
```

Now we need to run it once to process the config file and remove secrets (It will say it has done this and exit on first run)

    /usr/local/bin/mmmpuppet.py

check it out now

    cat $HOME/.config/mmm/conf.json

If it doesn’t change the file to remove all the linebreaks then it didn’t like it. Figure out why by looking at the log file.

    cat ~/.config/mmm/mmmpuppet.log

Go fix whatever went wrong. Which should be nothing. You should have seen a message like this as output before it returns you to a prompt:

    Login successful. Config updated with token. Run again to start bridge.

### Set up MMS bridge service

Make systemd unit

```
sudo vi /usr/lib/systemd/user/mmmpuppet.service

Description=Starts mmmpuppet interface
After=mmsd-mm.service

ExecStart=/usr/bin/python3 /usr/local/bin/mmmpuppet.py
Restart=on-failure
RestartSec=10s

WantedBy=default.target
```

and start it

```
sudo chmod 644 /usr/lib/systemd/user/mmmpuppet.service
systemctl enable mmmpuppet.service --user
systemctl start mmmpuppet.service --user
```

See if services are running:

    ps aux | grep mm

It should show something like this even after reboot

```
alarm       6374  0.0  0.3 235364  7752 ?        Ssl  22:44   0:00 /usr/local/bin/mmsd -n -d
alarm       6825  9.8  2.7 224976 54188 ?        Ssl  22:52   0:05 /usr/bin/python3 /usr/local/bin/mmmpuppet.py
```

## Remove Chatty

For Arch use Pacman to remove Chatty.

Mobian:

    apt remove chatty

## Don't forget to enable data

You can get SMS but not MMS with mobile data off

## Launch fractal

Log in with this homeserver

    http://localhost:8008

username `pp` and password `pp`

Logins are not saved. You need to add a new item named login to the gnome keyring manually to fix it. See: https://wiki.mobian.org/doku.php?id=fractal

Basically apt install seahorse, open "passwords and keys" in the app drawer, click new (plus), select password keyring, and name it "login" (all lower no quotes). Then autologin will work as it should.

## Done

At this point if you get a message a new room should be created by the bridge bot which you will be invited to. You can start a new conversation by creating a new room, setting the topic with phone numbers of participants, and then inviting the mm user. See the mmmpuppet readme for examples.

## Other clients

**quaternion** also seems to work but has clunky UI issues. Might work better with scaling
