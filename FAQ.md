# AdGuard Home - FAQ

## Questions:

* [After installing AdGuard Home, how to change dashboard interface's address?](#q1)
* [How to configure AdGuard Home to run together with pixelsrv-tls?](#q2)
* [Are there any known limitations?](#q3)
* [Why am I getting "bind: address already in use" error when trying to install on Ubuntu?](#q4)

## Answers:

<a id="q1"></a>

### After installing AdGuard Home, how to change dashboard interface's address?

1. Open `AdGuardHome.yaml` in the text editor
2. Modify `bind_host:` value to set a new network interface, e.g.:

	* 0.0.0.0: listen on all network interfaces
	* 127.0.0.1: listen on localhost only

3. Modify `bind_port:` value to set a new port
4. Restart AdGuard Home:

	`./AdGuardHome -s restart`


<a id="q2"></a>

### How to configure AdGuard Home to run together with pixelsrv-tls?

1. Open dashboard
2. Go to `Settings` -> `DNS settings`
3. Scroll to `DNS server configuration` section
4. For `Blocking mode` setting select `Custom IP` radio button and enter the IP address of pixelsrv-tls instance running.
5. Click `Save`


<a id="q3"></a>

### Are there any known limitations?

Here are some examples of what cannot be blocked by a DNS-level blocker:

    YouTube, Twitch ads
    Facebook, Twitter, Instagram sponsored posts

Essentially, any advertising that shares a domain with content cannot be blocked by a DNS-level blocker.

Is there a chance to handle this in the future?

DNS will never be enough to do this.
Our only option is to use a content blocking proxy like what we do in the standalone AdGuard applications.
We're going to bring this feature support to AdGuard Home in the future.
Unfortunately, even in this case, there still will be cases when this won't be enough or would require quite complicated configuration.


<a id="q4"></a>

### Why am I getting "bind: address already in use" error when trying to install on Ubuntu?

Because port 53 which is used for DNS is already occupied by another program.

Ubuntu comes with a local DNS server by default - "systemd-resolved" which uses 53 port and thus prevents AdGuard Home from binding to it.  To fix this, you should disable "systemd-resolved" daemon.  Luckily, AdGuard Home can detect such configurations and disable "systemd-resolved" for you if you press "Fix" button which is shown near to "address already in use" message.

But if you're using AdGuard Home with docker, you need to do it yourself.  Follow these steps:

Deactivate DNSStubListener and update DNS server address.  Create a new file: `/etc/systemd/resolved.conf.d/adguardhome.conf` (create a `/etc/systemd/resolved.conf.d` directory if necessary):

    [Resolve]
    DNS=127.0.0.1
    DNSStubListener=no

Specifying "127.0.0.1" as DNS server address is necessary because otherwise the nameserver will be "127.0.0.53" which doesn't work without DNSStubListener.

Activate another resolv.conf file:

    mv /etc/resolv.conf /etc/resolv.conf.backup
    ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

Stop DNSStubListener:

    systemctl reload-or-restart systemd-resolved
