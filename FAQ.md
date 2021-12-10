 #  AdGuard Home - FAQ

 *  [Why AdGuard Home doesn't block ads?](#doesntblock)
 *  [How to configure AdGuard Home to write verbose-level logs](#verboselog)
 *  [After installing AdGuard Home, how to change dashboard interface's address?](#webaddr)
 *  [How to set up AdGuard Home as default DNS server?](#defaultdns)
 *  [How to configure AdGuard Home to run together with pixelsrv-tls?](#pixelsrv)
 *  [Are there any known limitations?](#limitations)
 *  [Why am I getting "bind: address already in use" error when trying to install on Ubuntu?](#bindinuse)
 *  [How to configure a reverse proxy server for AdGuard Home?](#reverseproxy)
 *  [How to fix “permission denied” errors on Fedora?](#fedora)
 *  [How to uninstall AdGuard Home?](#uninstall) 



##  <a href="#doesntblock" id="doesntblock" name="doesntblock">Why AdGuard Home doesn't block ads?</a>

Suppose that AdGuard Home must block `somebadsite.com` but for some reason it
doesn't.  Let's try to resolve this issue.

Most likely you didn't configure your device to use AdGuard Home as its default
DNS server.  To check if you're using AdGuard Home as the default DNS server:

1.  On Windows, open a Terminal window (Start → Run → `cmd.exe`).  On other
    systems, open your Terminal application.

1.  Execute `nslookup example.org`.  It will print something like this:

    ```none
    Server:         192.168.0.1
    Address:        192.168.0.1#53

    Non-authoritative answer:
    Name:   example.org
    Address: <IPv4>
    Name:   example.org
    Address: <IPv6>
    ```

1.  Check if the `Server` IP address is the one on which AdGuard Home is
    running.  If no, then you need to configure your device, see
    [below](#defaultdns).

1.  Ensure that your request to `example.org` appears in the AdGuard Home UI on
    the Query Log page.  If not, then you need to configure AdGuard Home to
    listen on the specified network interface.  The most straightforward way to
    do so is to reinstall AdGuard Home with default settings.

Now that you are sure that your device uses AdGuard Home as its default DNS
server, the problem might be in AdGuard Home misconfiguration.  Please check and
ensure that:

1.  You have the “Block domains using filters and hosts files” setting enabled
    on the “Settings → General settings” page.

1.  You have the appropriate safety mechanisms, such as parental control,
    enabled on the “Settings → General settings”.

1.  You have the appropriate filters enabled on the “Filters → DNS blocklists”
    page.

1.  You don't have any filters that may interfere enabled on the “Filters → DNS
    allowlists” page.

1.  You don't have any DNS rewrites that may interfere on the “Filters → DNS
    rewrites” page.

1.  You don't have any custom filtering rules that may interfere on the “Filters
    → Custom filtering rules” page.



##  <a href="#verboselog" id="verboselog" name="verboselog">How to configure AdGuard Home to write verbose-level logs?</a>

To troubleshoot a complicated issue, the verbose-level logging is sometimes
required.  Here's how to enable it:

1.  Stop AdGuard Home:

    ```sh
    ./AdGuardHome -s stop
    ```

1.  Configure AdGuard Home to write verbose-level logs:

    1.  Open `AdGuardHome.yaml` in your editor.

    1.  Set `log_file` to the desired path of the log file, for example
        `/tmp/aghlog.txt`.  Note that the directory must exist.

    1.  Set `verbose` to `true`.

1.  Restart AdGuard Home and reproduce the issue:

    ```sh
    ./AdGuardHome -s start
    ```



## <a href="#webaddr" id="webaddr" name="webaddr">After installing AdGuard Home, how to change dashboard interface's address?</a>

1.  Stop AdGuard Home:

    ```sh
    ./AdGuardHome -s stop
    ```

1.  Open `AdGuardHome.yaml` in your editor.

1.  Set the `bind_host` setting to a new network interface.  For example:

     *  `0.0.0.0` to listen on all network interfaces.

     *  `127.0.0.1` to listen on the local loopback interface only.

1.  Optionally, set the `bind_port` setting to set a new port.

1.  Restart AdGuard Home:

    ```sh
    ./AdGuardHome -s start
    ```



##  <a href="#defaultdns" id="defaultdns" name="defaultdns">How to set up AdGuard Home as default DNS server?</a>

   ###  Router

This setup will automatically cover all devices connected to your home router,
and you won't need to configure each of them manually.

1.  Open the preferences for your router. Usually, you can access it from your
    browser via a URL, such as <http://192.168.0.1/> or <http://192.168.1.1/>.
    You may be prompted to enter a password.  If you don't remember it, you can
    often reset the password by pressing a button on the router itself, but be
    aware that if this procedure is chosen, you will probably lose the entire
    router configuration.  If your router requires an app to set it up, please
    install the app on your phone or PC and use it to access the router’s
    settings.

1.  Find the DHCP/DNS settings. Look for the DNS letters next to a field which
    allows two or three sets of numbers, each broken into four groups of one to
    three digits.

1.  Enter your AdGuard Home server addresses there.

1.  On some router types, a custom DNS server cannot be set up. In that case,
    setting up AdGuard Home as a DHCP server may help. Otherwise, you should
    check the router manual on how to customize DNS servers on your specific
    router model.



   ###  Windows

1.  Open Control Panel through Start menu or Windows search.

1.  Go to Network and Internet category and then to Network and Sharing Center.

1.  On the left side of the screen find “Change adapter settings” and click on
    it.

1.  Select your active connection, right-click on it and choose Properties.

1.  Find “Internet Protocol Version 4 (TCP/IPv4)” (or, for IPv6, “Internet
    Protocol Version 6 (TCP/IPv6)”) in the list, select it and then click on
    Properties again.

1.  Choose “Use the following DNS server addresses” and enter your AdGuard Home
    server addresses.



   ###  macOS

1.  Click on Apple icon and go to System Preferences.

1.  Click on Network.

1.  Select the first connection in your list and click Advanced.

1.  Select the DNS tab and enter your AdGuard Home server addresses.



   ###  Android

1.  From the Android Menu home screen, tap Settings.

1.  Tap Wi-Fi on the menu.  The screen listing all of the available networks
    will be shown (it is impossible to set custom DNS for mobile connection).

1.  Long press the network you're connected to and tap Modify Network.

1.  On some devices, you may need to check the box for Advanced to see further
    settings.  To adjust your Android DNS settings, you will need to switch the
    IP settings from DHCP to Static.

1.  Change set DNS 1 and DNS 2 values to your AdGuard Home server addresses.



   ###  iOS

1.  From the home screen, tap Settings.

1.  Choose Wi-Fi in the left menu (it is impossible to configure DNS for mobile
    networks).

1.  Tap on the name of the currently active network.

1.  In the DNS field enter your AdGuard Home server addresses.



##  <a href="#pixelsrv" id="pixelsrv" name="pixelsrv">How to configure AdGuard Home to run together with pixelsrv-tls?</a>

1.  Open the web UI.

1.  Go to “Settings → DNS settings”.

1.  Scroll to the “DNS server configuration” section.

1.  For the “Blocking mode” setting select the “Custom IP” radio button and
    enter the IP address of the pixelsrv-tls instance.

1.  Click “Save”.



##  <a id="limitations" name="limitations">Are there any known limitations?</a>

Here are some examples of what cannot be blocked by a DNS-level blocker:

 *  YouTube, Twitch ads.
 *  Facebook, Twitter, Instagram sponsored posts.

Essentially, any advertising that shares a domain with content cannot be blocked
by a DNS-level blocker.



   ###  Is there a chance to handle this in the future?

DNS will never be enough to do this.  Your only option is to use a content
blocking proxy like what we do in the standalone AdGuard applications.  We're
going to bring this feature support to AdGuard Home in the future.
Unfortunately, even in this case, there still will be cases when this won't be
enough or would require quite complicated configuration.



##  <a href="#bindinuse" id="bindinuse" name="bindinuse">Why am I getting `bind: address already in use` error when trying to install on Ubuntu?</a>

Because `127.0.0.1:53`, which is used for DNS, is already occupied by another
program.

**The easiest solution would be simply to choose a different network interface
and bind it to your external IP (for instance, your Wi-Fi IP).**

If for some reason you really want to bind to `127.0.0.1`, read the explanation
below.

Ubuntu comes with a local DNS server by default called `systemd-resolved`, which
uses port 53 and thus prevents AdGuard Home from binding to it.  To fix this,
disable the `systemd-resolved` daemon.  Luckily, AdGuard Home can detect such
configurations and disable `systemd-resolved` for you if you press "Fix" button
which is shown near the `address already in use` message.

Note that if you're using AdGuard Home with docker or snap, you'll have to do it
yourself by following these steps:

1.  Deactivate `DNSStubListener` and update DNS server address.  Create a new
    file: `/etc/systemd/resolved.conf.d/adguardhome.conf` (create
    a `/etc/systemd/resolved.conf.d` directory if necessary) with the following
    content:

    ```none
    [Resolve]
    DNS=127.0.0.1
    DNSStubListener=no
    ```

1.  Specifying `127.0.0.1` as DNS server address **is necessary** because
    otherwise the nameserver will be `127.0.0.53` which doesn't work without
    `DNSStubListener`.

1.  Activate another `resolv.conf` file:

    ```sh
    sudo mv /etc/resolv.conf /etc/resolv.conf.backup
    ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
    ```

1.  Restart `DNSStubListener`:

    ```sh
    systemctl reload-or-restart systemd-resolved
    ```



##  <a href="#reverseproxy" id="reverseproxy" name="reverseproxy">How to configure a reverse proxy server for AdGuard Home?</a>

If you're running a web server already and you want to access AdGuard Home
dashboard UI from an URL like `http://YOUR_SERVER/aghome/` you can use this
configuration for your web server:

   ###  nginx

```none
location /aghome/ {
    proxy_pass http://AGH_IP:AGH_PORT/;
    proxy_redirect / /aghome/;
    proxy_cookie_path / /aghome/;
}
```



   ###  caddy

```none
:80/aghome/* {
    route {
        uri strip_prefix /aghome
        reverse_proxy AGH_IP:AGH_PORT
    }
}
```

Or, if you just want to serve AdGuard Home with automatic TLS, something similar
to:

```none
DOMAIN {
    encode gzip zstd
    tls YOUR_EMAIL@DOMAIN
    reverse_proxy AGH_IP:AGH_PORT
}
```



   ###  Disable DoH encryption on AdGuard Home

When you use TLS on your reverse proxy server, there's no need to use TLS on
AdGuard Home.  Set `allow_unencrypted_doh: false` in `AdGuardHome.yaml` to allow
AdGuard Home respond to DoH requests without TLS encryption.



##  <a href="#fedora" id="fedora" name="fedora">How to fix `permission denied` errors on Fedora?</a>

1.  Move the `AdGuardHome` binary to `/usr/local/bin`.

1.  As `root`, execute the following command to change the security context of
    the file:

    ```sh
    chcon -t bin_t /usr/local/bin/AdGuardHome
    ```

1.  Add the required firewall rules in order to make it reachable through the
    network.  For example:

    ```sh
    firewall-cmd --new-zone=adguard --permanent
    firewall-cmd --zone=adguard --add-source=192.168.0.14/24 --permanent
    firewall-cmd --zone=adguard --add-port=3000/tcp --permanent
    firewall-cmd --zone=adguard --add-port=53/udp --permanent
    firewall-cmd --zone=adguard --add-port=80/tcp --permanent
    firewall-cmd --reload
    ```

See [issue 765].

[issue 765]: https://github.com/AdguardTeam/AdGuardHome/issues/765#issuecomment-752262353



##  <a href="#uninstall" id="uninstall" name="uninstall">How to uninstall AdGuard Home?</a>

The way to uninstall AdGuard Home depends on how you installed it.

**IMPORTANT:** After uninstalling AdGuard Home, don't forget to change your
devices configuration and point them to a different DNS server.

   ###  Regular installation

In this case you need to do the following:

 *  Unregister AdGuard Home service: `./AdGuardHome -s uninstall`.

 *  Remove the AdGuard Home directory.



   ###  Docker

Simply stop and remove the image.



   ###  Snap Store

```sh
snap remove adguard-home
```
