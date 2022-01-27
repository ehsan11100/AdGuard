 #  AdGuard Home - FAQ

 *  [Why AdGuard Home doesn't block ads?](#doesntblock)
 *  [Where can I inspect the logs?](#logs)
 *  [How to configure AdGuard Home to write verbose-level logs?](#verboselog)
 *  [How to show a custom block page?](#customblock)
 *  [How to change dashboard interface's address?](#webaddr)
 *  [How to set up AdGuard Home as default DNS server?](#defaultdns)
 *  [Are there any known limitations?](#limitations)
 *  [Why am I getting `bind: address already in use` error when trying to install on Ubuntu?](#bindinuse)
 *  [How to configure a reverse proxy server for AdGuard Home?](#reverseproxy)
 *  [How to fix `permission denied` errors on Fedora?](#fedora)
 *  [How to fix `incompatible file system` errors?](#incompatfs)
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
    running.  If not, you need to configure your device, see
    [below](#defaultdns).

1.  Ensure that your request to `example.org` appears in the AdGuard Home UI on
    the Query Log page.  If not, you need to configure AdGuard Home to listen on
    the specified network interface.  The most straightforward way to do so is
    to reinstall AdGuard Home with default settings.

If you are sure that your device uses AdGuard Home as its default DNS server,
but the problem persists, it might have something to do with an AdGuard Home
misconfiguration.  Please check and ensure that:

1.  You have the “Block domains using filters and hosts files” setting enabled
    on the “Settings → General settings” page.

1.  You have the appropriate safety mechanisms, such as Parental Control,
    enabled on the “Settings → General settings”.

1.  You have the appropriate filters enabled on the “Filters → DNS blocklists”
    page.

1.  You don't have any exception rule lists that may allow the requests enabled
    on the “Filters → DNS allowlists” page.

1.  You don't have any DNS rewrites that may interfere with the “Filters → DNS
    rewrites” page.

1.  You don't have any custom filtering rules that may interfere with the
    “Filters → Custom filtering rules” page.



##  <a href="#logs" id="logs" name="logs">Where can I inspect the logs?</a>

The default location of the plain-text logs (not to be confused with the query
logs) depends on the operating system and installation mode:

<!--
    TODO(a.garipov): Add more info about Docker; add links to tools.
-->

 *  **OpenWrt Linux:** use the `logread -e AdGuardHome` command.

 *  **Linux** systems with **systemd** and other **Unix** systems with
    **SysV-style init** including **macOS:** `/var/log/AdGuardHome.err`.

 *  **Linux** systems with **Snapcraft** use the `snap logs adguard-home`
    command.

 *  **FreeBSD** and **OpenBSD:** currently, no logs are written by default.
    That will change in the future, see [issue 4213].

 *  On **Windows:** the [Windows Event Log][wlog] is used.

[wlog]:       https://docs.microsoft.com/en-us/windows/win32/wes/windows-event-log
[issue 4213]: https://github.com/AdguardTeam/AdGuardHome/issues/4213



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



<!-- There are still links to the #pixelsrv section which was merged into this
one on the Internet, and Cool URIs Don't Change, so use an additional ID/name
anchor into this header. -->

## <a href="#customblock" id="customblock" name="customblock"><span id="pixelsrv" name="pixelsrv"/>How to show a custom block page?</a>

   ###  A note about HTTPS

Before doing any of this, please note that modern browsers are set up to use
HTTPS, so they validate the authenticity of the web server certificate.  That
means that using any of these will result in warning screens.

There are a couple of proposed extensions that, when they become reasonably well
supported by clients, would allow for a better user experience, including the
[RFC 8914 Extended DNS Error codes][rfc8914] and the [DNS Access Denied Error
Page RFC draft][rfcaccess].  We'll implement them when browsers actually start
to support them.

[rfc8914]:   https://datatracker.ietf.org/doc/html/rfc8914
[rfcaccess]: https://datatracker.ietf.org/doc/html/draft-reddy-dnsop-error-page-08



   ###  Prerequisites

To use any of these ways to show a custom block page, you'll need an HTTP server
running on some IP address and serving the page in question on all routes.
Something like [`pixelserv-tls`][pxsrv].

[pxsrv]: https://github.com/kvic-z/pixelserv-tls



   ###  Custom block page for Parental Control and Safe Browsing filters

There is currently no way to set these parameters from the UI, so you'll need to
edit the configuration file manually:

1.  Stop AdGuard Home:

    ```sh
    ./AdGuardHome -s stop
    ```

1.  Open `AdGuardHome.yaml` in your editor.

1.  Set the `dns.parental_block_host` or `dns.safebrowsing_block_host` settings
    to the IP address of the server (in this example, `192.168.123.45`):

    ```yaml
    # …
    dns:
      # …

      # NOTE: Change to the actual IP address of your server.
      parental_block_host: 192.168.123.45
      safebrowsing_block_host: 192.168.123.45
    ```

1.  Restart AdGuard Home:

    ```sh
    ./AdGuardHome -s start
    ```



   ###  Custom block page for other filters

1.  Open the web UI.

1.  Open the “Settings → DNS settings” page.

1.  In the “DNS server configuration” section, select the “Custom IP” radio
    button in the “Blocking mode” selector and enter the IPv4 and IPv6 addresses
    of the server.

1.  Click “Save”.



## <a href="#webaddr" id="webaddr" name="webaddr">How to change dashboard interface's address?</a>

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

See the [“Configuring Devices” section](Getting-Started#configure-devices) on
the “Getting Started” page.



##  <a href="#limitations" id="limitations" name="limitations">Are there any known limitations?</a>

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

This happens because the address `127.0.0.1:53`, which is used for DNS, is
already taken by another program.

**The easiest solution would be simply to choose a different network interface
and bind it to an accessible IP address (for instance, the IP address of your
router inside your network).**

If you need AdGuard Home to accept connections on `127.0.0.1`, read the
explanation below.

Ubuntu comes with a local DNS server by default called `systemd-resolved`, which
uses port 53 and thus prevents AdGuard Home from binding to it.  To fix this,
disable the `systemd-resolved` daemon.  Luckily, AdGuard Home can detect such
configurations and disable `systemd-resolved` for you if you press the “Fix”
button, which is shown near the `address already in use` message.

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
    sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
    ```

1.  Restart `DNSStubListener`:

    ```sh
    systemctl reload-or-restart systemd-resolved
    ```



##  <a href="#reverseproxy" id="reverseproxy" name="reverseproxy">How to configure a reverse proxy server for AdGuard Home?</a>

If you're already running a web server and want to access the AdGuard Home
dashboard UI from a URL like `http://YOUR_SERVER/aghome/`, you can use this
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

Or, if you just want to serve AdGuard Home with automatic TLS, use
a configuration similar to the example shown below:

```none
DOMAIN {
    encode gzip zstd
    tls YOUR_EMAIL@DOMAIN
    reverse_proxy AGH_IP:AGH_PORT
}
```



   ###  Disable DoH encryption on AdGuard Home

When you use TLS on your reverse proxy server, there's no need to use TLS on
AdGuard Home.  Set `allow_unencrypted_doh: true` in `AdGuardHome.yaml` to allow
AdGuard Home respond to DoH requests without TLS encryption.



   ###  Real IP addresses of clients

**Since v0.107.0,** you can set the parameter `trusted_proxies` to the IP
address(es) of your HTTP proxy to make AdGuard Home take the headers containing
the real client IP address into account.  See the [configuration page][conf] for
more information.

[conf]: https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration



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

If you are still getting `code=exited status=203/EXEC` or similar errors from
`systemctl`, try uninstalling AdGuard Home and installing *directly* into
`/usr/local/bin` by using the `-o` option of the install script:

```sh
curl -s -S -L 'https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh' | sh -s -- -o '/usr/local/bin' -v
```

See [issue 765] and [issue 3281].

[issue 3281]: https://github.com/AdguardTeam/AdGuardHome/issues/3281
[issue 765]:  https://github.com/AdguardTeam/AdGuardHome/issues/765#issuecomment-752262353



##  <a href="#incompatfs" id="incompatfs" name="incompatfs">How to fix `incompatible file system` errors?</a>

You should move your AdGuard Home installation or working directory to another
location.  See the [limitations section](Getting-Started#limitations) on the
“Getting Started” page.



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
