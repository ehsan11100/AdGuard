# How to set up your own AdGuard Home on Raspberry Pi

You can install AdGuard Home on your [Raspberry Pi](https://www.raspberrypi.org) and use it to filter out ads. This also saves data traffic.

Afterwards you can use your AdGuard Home on any machine connected to the same local network.

This is different from browser-based ad blocking extension because it works not only in a browser but in other apps too.

There are many reasons to remove ads:

 * They can be really annoying.
 * They can be really intrusive.
 * Sometimes they are [laced with scripts for bitcoin mining](https://arstechnica.com/information-technology/2018/01/now-even-youtube-serves-ads-with-cpu-draining-cryptocurrency-miners/).
 * Bad people can [extract sensitive information](https://www.theverge.com/2017/12/30/16829804/browser-password-manager-adthink-princeton-research) from your browser with a nasty malicious script.
 * If you are on metered bandwidth, it can offer significant data savings when browsing the internet in Chrome, Firefox, Edge or Safari.

## Prepare Raspberry Pi

If you haven't already done so, set up your Raspberry Pi by preparing the SD card, then connecting keyboard, mouse and display/TV via HDMI, then connecting it to a power supply and booting it. An official guide to do that is here — https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up

You will not need a keyboard and mouse for very long, just to type a few commands and then write down the results.

Once you've booted into the desktop, you need to connect it to network:

 * If you have a model that has built-in WiFi or you have USB WiFi dongle, follow this guide — https://www.raspberrypi.org/documentation/configuration/wireless/desktop.md
 * If you have ethernet/LAN adapter and LAN connectivity, just plug it into your Pi.

Next, we will enable SSH, so you can comfortably access it from your main computer. Open Terminal on your Pi and type this:
```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

This should enable SSH, next up, we need to note down the IP address that has been assigned to your Pi. 

Without closing Terminal, type this:
```bash
hostname -I|xargs -n1
```

It will output an IP address, if there are several, note down only first one, rest you can keep just in case.

For illustration purposes, I will use `192.168.10.20` in examples — don't forget to substitute it with the one you've noted down.

Switch back to your main computer, but keep your Pi running, and launch an ssh client:

 * On Windows, we recommend [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) and then connect it to your Raspberry Pi's IP address (which is `192.168.10.20` in my network) with username `pi` and your Pi's password (which is `raspberry` unless you've changed it already).
 * On Linux or MacOS, just open Terminal and type `ssh pi@192.168.10.20`, then type your Pi's password (which is `raspberry` unless you've changed it already).

Once you're done and everything went successfully, you will be greeted by Raspberry Pi's command line interface and you're ready to start installing your own AdGuard Home!

![wcmbkfejpl](https://user-images.githubusercontent.com/739119/46160233-d931e000-c28a-11e8-84c0-a2721b9d6b98.png)

## Installing AdGuard Home on Pi

Go to [AdGuard Home page](https://github.com/AdguardTeam/AdGuardHome#installation) and download binaries for Raspberry Pi.

At the time of writing this guide, the latest version is v0.95.

Let's download AdGuard Home and unpack it:
```bash
cd $HOME
wget https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.95/AdGuardHome_v0.95_linux_arm.tar.gz
tar xvf AdGuardHome_v0.95_linux_arm.tar.gz
```
It will unpack into a new directory called `AdGuardHome`.

All you have to do is simply type this command to the Terminal:

```bash
sudo ./AdGuardHome -s install
```

Here are the other commands you might need to control the service.

* `AdGuardHome -s uninstall` - uninstalls the AdGuard Home service.
* `AdGuardHome -s start` - starts the service.
* `AdGuardHome -s stop` - stops the service.
* `AdGuardHome -s restart` - restarts the service.
* `AdGuardHome -s status` - shows the current service status.

## Visit the web interface
Once it's up and running, you can access your AdGuard Home web interface on port 3000 by typing this in your browser — `http://192.168.10.20:3000/`

Don't forget to substitute `192.168.10.20` with IP of your Pi.

## Verify DNS filtering
You can verify that it's working properly by running this on your Pi:
```bash
host doubleclick.net 127.0.0.1
```

If everything works correctly, you will get this output:
```bash
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases:

Host doubleclick.net not found: 3(NXDOMAIN)
```

## Configure your devices to use your AdGuard Home

Now, once we've established that AdGuard Home works on our Raspberry Pi, you can use it on other computers in your network by changing their system DNS settings to use Pi's IP address (which is `192.168.10.20` in our case).

<!-- TODO: link to guides or provide a short guide here -->

## (Optionally) password-protect web interface

You have an option to password-protect your AdGuard Home's web interface so only you can access it.

To do so, stop it first:
```bash
sudo ./AdGuardHome -s stop
```

Then edit the configuration file:
```bash
sudo nano /home/pi/AdGuardHome/AdGuardHome.yaml
```

Find lines containing `auth_name: ""` and `auth_pass: ""` and replace them with username and password:
```ini
auth_name: "your-secret-name"
auth_pass: "your-secret-password"
```

Substitute name and password with your own, of course.

After you're done editing, save the file and start your AdGuard Home again:
```bash
sudo ./AdGuardHome -s start
```

After that, visiting web interface in a browser will ask for username and password.