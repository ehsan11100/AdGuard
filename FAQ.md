 # AdGuard Home - FAQ

## Questions:

* [Why AdGuard Home doesn't block ads?](#doesntblock)
* [After installing AdGuard Home, how to change dashboard interface's address?](#webaddr)
* [How to set up AdGuard Home as default DNS server?](#defaultdns)
* [How to configure AdGuard Home to run together with pixelsrv-tls?](#pixelsrv)
* [Are there any known limitations?](#limitations)
* [Why am I getting "bind: address already in use" error when trying to install on Ubuntu?](#bindinuse)
* [How to configure a reverse proxy server for AdGuard Home?](#reverseproxy)
* [How to fix “permission denied” errors on Fedora?](#fedora)
* [How to uninstall AdGuard Home?](#uninstall) 

## Answers:

### <a href="#doesntblock" id="doesntblock">Why AdGuard Home doesn't block ads?</a>

Suppose you expect that AdGuard Home must block `somebadsite.com` but for some reason it doesn't.  Let's try to resolve this issue.

Most likely you didn't configure your device to use AdGuard Home as default DNS server.  To check if you're using AdGuard Home as default DNS server:

1. On OS Windows open Terminal window (Start -> Run -> `cmd.exe`).  On other systems open your Terminal application.

2. Execute `nslookup example.org`.  It will print something like this:

        Server:         192.168.0.1
        Address:        192.168.0.1#53

        Non-authoritative answer:
        Name:   example.org
        Address: <IPv4>
        Name:   example.org
        Address: <IPv6>

3. Check if `Server` IP is the same IP AdGuard Home is running on.  If no, then you need to configure your device, see https://github.com/AdguardTeam/AdGuardHome/wiki/FAQ#defaultdns.

4. Ensure your request to `example.org` appears in AdGuard Home UI on page `Query logs`.  If not, then you need to configure AdGuard Home to listen on the specified network interface.  The most straightforward way to do so is to reinstall AdGuard Home with default settings.

Now that you are sure that your device uses AdGuard Home as default DNS server, the problem might be in AdGuard Home misconfiguration.  Please check and ensure that:

1. You have `Block domains using filters and hosts files` setting enabled in `Settings -> General settings`.

2. You have the appropriate safe services enabled in `Settings -> General settings`.

3. You have the appropriate filters enabled in `Filters -> DNS blocklists`.

4. You have no filters enabled in `Filters -> DNS allowlists` that may interfere.

5. You have no DNS rewrites in `Filters -> DNS rewrites` that may interfere.

6. You have no custom rules in `Filters -> Custom filtering rules` that may interfere.



### <a href="#webaddr" id="webaddr">After installing AdGuard Home, how to change dashboard interface's address?</a>

1. Open `AdGuardHome.yaml` in the text editor
2. Modify `bind_host:` value to set a new network interface, e.g.:

	* 0.0.0.0: listen on all network interfaces
	* 127.0.0.1: listen on localhost only

3. Modify `bind_port:` value to set a new port
4. Restart AdGuard Home:

	`./AdGuardHome -s restart`



### <a href="#defaultdns" id="defaultdns">How to set up AdGuard Home as default DNS server?</a>

#### Router

This setup will automatically cover all the devices connected to your home router and you will not need to configure each of them manually.

1. Open the preferences for your router. Usually, you can access it from your browser via a URL (like http://192.168.0.1/ or http://192.168.1.1/). You may be asked to enter the password. If you don't remember it, you can often reset the password by pressing a button on the router itself. Some routers require a specific application, which in that case should be already installed on your computer/phone.
2. Find the DHCP/DNS settings. Look for the DNS letters next to a field which allows two or three sets of numbers, each broken into four groups of one to three digits.
3. Enter your AdGuard Home server addresses there.
4. You can't set a custom DNS server on some types of routers. In this case it may help if you set up AdGuard Home as a DHCP server. Otherwise, you should search for the manual on how to customize DNS servers for your particular router model.

#### Windows

1. Open Control Panel through Start menu or Windows search.
2. Go to Network and Internet category and then to Network and Sharing Center.
3. On the left side of the screen find Change adapter settings and click on it.
4. Select your active connection, right-click on it and choose Properties.
5. Find Internet Protocol Version 4 (TCP/IP) in the list, select it and then click on Properties again.
6. Choose Use the following DNS server addresses and enter your AdGuard Home server addresses.

#### macOS

1. Click on Apple icon and go to System Preferences.
2. Click on Network.
3. Select the first connection in your list and click Advanced.
4. Select the DNS tab and enter your AdGuard Home server addresses.

#### Android

1. From the Android Menu home screen, tap Settings.
2. Tap Wi-Fi on the menu. The screen listing all of the available networks will be shown (it is impossible to set custom DNS for mobile connection).
3. Long press the network you're connected to, and tap Modify Network.
4. On some devices, you may need to check the box for Advanced to see further settings. To adjust your Android DNS settings, you will need to switch the IP settings from DHCP to Static.
5. Change set DNS 1 and DNS 2 values to your AdGuard Home server addresses.

#### iOS

1. From the home screen, tap Settings.
2. Choose Wi-Fi in the left menu (it is impossible to configure DNS for mobile networks).
3. Tap on the name of the currently active network.
4. In the DNS field enter your AdGuard Home server addresses.



### <a href="#pixelsrv" id="pixelsrv">How to configure AdGuard Home to run together with pixelsrv-tls?</a>

1. Open dashboard
2. Go to `Settings` -> `DNS settings`
3. Scroll to `DNS server configuration` section
4. For `Blocking mode` setting select `Custom IP` radio button and enter the IP address of pixelsrv-tls instance running.
5. Click `Save`



### <a id="limitations">Are there any known limitations?</a>

Here are some examples of what cannot be blocked by a DNS-level blocker:

 *  YouTube, Twitch ads
 *  Facebook, Twitter, Instagram sponsored posts

Essentially, any advertising that shares a domain with content cannot be blocked by a DNS-level blocker.

Is there a chance to handle this in the future?

DNS will never be enough to do this.
Our only option is to use a content blocking proxy like what we do in the standalone AdGuard applications.
We're going to bring this feature support to AdGuard Home in the future.
Unfortunately, even in this case, there still will be cases when this won't be enough or would require quite complicated configuration.



### <a href="#bindinuse" id="bindinuse">Why am I getting "bind: address already in use" error when trying to install on Ubuntu?</a>

Because 127.0.0.1:53 which is used for DNS is already occupied by another program.

**The easiest solution would be simply to choose a different network interface and bind it to your external IP (for instance, your Wi-Fi IP).**

If for some reason you really want to bind to `127.0.0.1`, read the explanation below.

Ubuntu comes with a local DNS server by default - "systemd-resolved" which uses 53 port and thus prevents AdGuard Home from binding to it.  To fix this, you should disable "systemd-resolved" daemon.  Luckily, AdGuard Home can detect such configurations and disable "systemd-resolved" for you if you press "Fix" button which is shown near to "address already in use" message.

But if you're using AdGuard Home with docker or snap, you need to do it yourself. 

Follow these steps:

1. Deactivate `DNSStubListener` and update DNS server address.  Create a new file: `/etc/systemd/resolved.conf.d/adguardhome.conf` (create a `/etc/systemd/resolved.conf.d` directory if necessary):

    ```
    [Resolve]
    DNS=127.0.0.1
    DNSStubListener=no
    ```

2. Specifying `127.0.0.1` as DNS server address **is necessary** because otherwise the nameserver will be `127.0.0.53` which doesn't work without `DNSStubListener`.

3. Activate another resolv.conf file:

    ```
    sudo mv /etc/resolv.conf /etc/resolv.conf.backup
    ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
    ```

4. Restart `DNSStubListener`:

    ```
    systemctl reload-or-restart systemd-resolved
    ```



### <a href="#reverseproxy" id="reverseproxy">How to configure a reverse proxy server for AdGuard Home?</a>

If you're running a web server already and you want to access AdGuard Home dashboard UI from an URL like `http://YOUR_SERVER/aghome/` you can use this configuration for your web server:

#### nginx

    location /aghome/ {
        proxy_pass http://AGH_IP:AGH_PORT/;
        proxy_redirect / /aghome/;
        proxy_cookie_path / /aghome/;
    }

#### caddy

    :80/aghome/* {
        route {
            uri strip_prefix /aghome
            reverse_proxy AGH_IP:AGH_PORT
        }
    }

#### Disable DoH encryption on AdGuard Home

When you use TLS on your reverse proxy server, there's no need to use TLS on AdGuard Home.
Set `allow_unencrypted_doh: false` in AdGuardHome.yaml to allow AdGuard Home respond to DoH requests without TLS encryption.



### <a href="#fedora" id="fedora">How to fix “permission denied” errors on Fedora?</a>

1.  Moved the `AdGuardHome` binary to `/usr/local/bin`.
1.  As `root`, execute the following command to change the security context of
    the file:
    ```
    # chcon -t bin_t /usr/local/bin/AdGuardHome
    ```
1.  Add the required firewall rules in order to make it reachable through the
    network.  For example:
    ```
    # firewall-cmd --new-zone=adguard --permanent
    # firewall-cmd --zone=adguard --add-source=192.168.0.14/24 --permanent
    # firewall-cmd --zone=adguard --add-port=3000/tcp --permanent
    # firewall-cmd --zone=adguard --add-port=53/udp --permanent
    # firewall-cmd --zone=adguard --add-port=80/tcp --permanent
    # firewall-cmd --reload
    ```

See [issue 765].

[issue 765]: https://github.com/AdguardTeam/AdGuardHome/issues/765#issuecomment-752262353



### <a href="#uninstall" id="uninstall">How to uninstall AdGuard Home?</a>

The way to uninstall AdGuard Home depends on how you installed it.

> **IMPORTANT:** After uninstalling AdGuard Home, don't forget to change your devices configuration and point them to a different DNS server.

#### Regular installation

In this case you need to do the following:

* Unregister AdGuard Home service: `./AdGuardHome -s uninstall`
* Remove AdGuard Home directory

#### Docker

Uninstalling is trivial, stop and remove the image.

#### Snap Store

Just one command: `snap remove adguard-home`