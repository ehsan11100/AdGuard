# AdGuard Home - Encryption

We are proud to say that AdGuard Home supports all modern DNS encryption protocols **out-of-the-box**:

* [DNS-over-HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS)
* [DNS-over-TLS](https://en.wikipedia.org/wiki/DNS_over_TLS)
* [DNSCrypt](https://dnscrypt.info/) (upstreams only)

In this guide we will explain how to setup your own "Secure DNS" server with AdGuard Home.

1. [Install AdGuard Home on your server](#install)
2. [Register a domain name](#register)
3. [Get an SSL certificate](#certificate)
    * [Install CertBot](#certbot)
    * [Get a certificate using DNS challenge](#certbot-dnschallenge)
4. [Configure AdGuard Home](#configure-home)
5. [Configure your devices](#configure-devices)

<a id="install"></a>
## Install AdGuard Home on your server

It does not make much sense to configure DNS encryption inside of your own local network. The purpose of securing your DNS traffic is to secure it from third-parties that might be analyzing or modifying it. For instance, from your ISP.

It means that you will need a server with a public dedicated IP address. There are plenty of cheap cloud servers providers: [DigitalOcean](https://digitalocean.com), [Vultr](https://vultr.com), [Linode](https://www.linode.com/), etc. Just choose one, create a cloud server there, and [install AdGuard Home](Getting-Started) on your server.

<a id="register"></a>
## Register a domain name

First of all, you need a domain name. If you have never registered one, here is a [simple instruction](https://www.pcworld.com/article/241722/web-apps/how-to-register-your-own-domain-name.html) that will help you with that.

<a id="certificate"></a>
## Get an SSL certificate

Both `DNS-over-HTTPS` and `DNS-over-TLS` are based on [TLS encryption](https://en.wikipedia.org/wiki/Transport_Layer_Security) so in order to use them, you will need to acquire an SSL certificate.

An SSL certificate can be bought from a "Certificate Authority" (CA), a company trusted by browsers and operating systems to enroll SSL certificates for domains.

Alternatively, you can get the certificate for free from ["Let's Encrypt" CA](https://letsencrypt.org/), a free certificate authority developed by the Internet Security Research Group (ISRG).

In this guide I'll explain how to get a certificate from them.

<a id="certbot"></a>
### Install CertBot

Certbot is an easy-to-use client that fetches a certificate from Let’s Encrypt.

1. Go to [https://certbot.eff.org/](https://certbot.eff.org/) and choose "None of the above" software and your operating system.
2. Follow the installation instructions, and stop there - don't get to the "Get Started" section.

<a id="certbot-dnschallenge"></a>
### Get a certificate using DNS challenge

You have just got a domain name so I suppose using DNS challenge will be the easiest way to get a certificate.

Run this command and follow the certbot's instructions:
```bash
sudo certbot certonly --manual --preferred-challenges=dns
```

In the end you'll get two files:
* `fullchain.pem` -- your PEM-encoded SSL certificate
* `privkey.pem` -- your PEM-encoded private key

Both will be necessary to configure AdGuard Home.

> You will need to use the very same procedure to renew the existing certificate.

<a id="configure-home"></a>
## Configure AdGuard Home

1. Open AdGuard Home web interface and go to settings.
2. Scroll down to the "Encryption" settings
    ![](https://user-images.githubusercontent.com/5947035/53301027-2a0c2b80-385f-11e9-81f3-bcc63de4eef1.png)
3. Copy/paste the contents of the `fullchain.pem` file to "Certificates"
4. Copy/paste the contents of the `privkey.pem` file to "Private key"
5. Enter your domain name to "Server name"
6. Click "Save settings".

<a id="configure-devices"></a>
## Configure your devices

Please note that encrypted DNS protocols are supported only on Android 9. So you need to install additional software for other operating systems.

Here's a list of software you can use.

### Android

* Android 9 supports DNS-over-TLS natively. To configure it, go to Settings → Network & internet → Advanced → Private DNS and enter your domain name there.
* [AdGuard for Android](https://adguard.com/en/adguard-android/overview.html) supports `DNS-over-HTTPS` and `DNS-over-TLS`.
* [Intra](https://getintra.org/) adds `DNS-over-HTTPS` support to Android.

### iOS

* [DNSCloak](https://itunes.apple.com/app/id1452162351) supports `DNS-over-HTTPS` but in order to configure it to use your own server, you'll need to generate a [DNS Stamp](https://dnscrypt.info/stamps) for it.
* [AdGuard for iOS](https://adguard.com/en/adguard-ios/overview.html) supports `DNS-over-HTTPS` and `DNS-over-TLS`.

### Other implementations

* AdGuard Home itself can be a secure DNS client on any platform.
* [dnsproxy](https://github.com/AdguardTeam/dnsproxy) supports all known secure DNS protocols.
* [dnscrypt-proxy](https://github.com/jedisct1/dnscrypt-proxy) supports `DNS-over-HTTPS`.
* [Mozilla Firefox](https://www.mozilla.org/firefox/) supports `DNS-over-HTTPS`.
* You will find more implementations [here](https://dnscrypt.info/implementations) and [here](https://dnsprivacy.org/wiki/display/DP/DNS+Privacy+Clients).
