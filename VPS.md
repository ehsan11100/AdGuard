To run AdGuard Home on a VPS, you need a server with Debian 8 or 9, x64 or x32.

## Initial installation

First let's ensure that your VPS has necessary minimal requirements, run this as root:
```bash
apt-get install sudo nano bind9-host
```

Go to [AdGuard Home page](https://github.com/AdguardTeam/AdGuardHome#installation) and download binaries for your architecture (64-bit Linux in this example).

As of the time of writing, the latest version is v0.9

To download AdGuard Home and unpack it execute following commands:
```bash
wget https://github.com/AdguardTeam/AdGuardHome/releases/download/v0.9/AdGuardHome_v0.9_linux_amd64.tar.gz
tar xvf AdGuardHome_v0.9_linux_amd64.tar.gz
```

You can find out the directory where you've unpacked it to by running these commands:
```bash
cd AdGuardHome
pwd
```

In this example it is `/root/AdGuardHome`, now let's make it run on VPS boot:
```bash
sudo nano /etc/systemd/system/adguard-home.service
```

A file will be opened, fill the file contents with this text:
```ini
[Unit]
Description=AdGuard Home
After=syslog.target
After=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/root/AdGuardHome
ExecStart=/root/AdGuardHome/AdGuardHome --host 0.0.0.0
Restart=always

[Install]
WantedBy=multi-user.target
```

Note that I've used `/root/AdGuardHome` in that file, replace both instances with your own values there. Write to the file and close it.

After you're done with that, let's enable and start AdGuard Home:
```bash
sudo systemctl enable adguard-home
sudo systemctl start adguard-home
```

You can verify that it's working properly by running this command:
```bash
host doubleclick.net 127.0.0.1
```

If everything works correctly, you will get this output:
```
Using domain server:
Name: 127.0.0.1
Address: 127.0.0.1#53
Aliases:

Host doubleclick.net not found: 3(NXDOMAIN)
```

## Visit the web interface
You can access your AdGuard Home web interface on port 3000 by typing this in your browser — `http://1.2.3.4:3000/`

Replace 1.2.3.4 with the IP address of your VPS.

## Configure your devices to use your AdGuard Home

Now, once you've established that AdGuard Home works on your VPS, you can use it on your machine by changing system DNS settings to use your VPS's public IP address.

## (optional) Password-protect web interface

You have an option to password-protect your AdGuard Home's web interface so only you can access it.

To do so, stop it first:
```bash
sudo systemctl stop adguard-home
```

Then edit `/root/AdGuardHome/AdGuardHome.yaml` (substitute this path with your own as appropriate):
```bash
sudo nano /root/AdGuardHome/AdGuardHome.yaml
```

Find lines containing `auth_name: ""` and `auth_pass: ""` and replace them with username and password:
```ini
auth_name: "your-secret-name"
auth_pass: "your-secret-password"
```

Substitute name and password with your own, of course.

After you're done editing, save the file and start your AdGuard Home again:
```bash
sudo systemctl start adguard-home
```

After that, visiting web interface in a browser will require entering username and password.