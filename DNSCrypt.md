 #  AdGuard Home - DNSCrypt

 *  [Generating A Configuration File](#generate-config)
 *  [Generating A *DNSCrypt* Stamp](#generate-stamp)
 *  [Configuring Devices To Use *DNSCrypt*](#configure-devices)

**Since v0.105.0**, AdGuard Home is able to work as a DNSCrypt server.  However,
this feature is only available via configuration file, you cannot set it up
using the Web UI.  This guide explains how to do this.

##  <a href="#generate-config" id="#generate-config" name="generate-config">Generating A Configuration File</a>

Here is how to generate a DNSCrypt configuration file and point AdGuard Home to
it:

1.  **Important!** Make sure that your TLS settings are valid and encryption is
    enabled.

1.  Get the latest version of the [`dnscrypt`] utility for your system.

1.  Run:

    ```sh
    dnscrypt generate --provider-name '2.dnscrypt-cert.example.org' --out ./dnscrypt.yaml
    ```

    Where `example.org` is the name of your host and `./dnscrypt.yaml` is the
    name of the configuration output file.

1.  Before changing the configuration file, it is important to **stop AdGuard
    Home**.  In your *AdGuardHome* configuration file (typically
    `AdGuardHome.yaml`), add the following lines:

    ```yaml
    'tls':
      # N.B. The encryption must be enabled.
      'enabled': true
      # …
      'port_dnscrypt': 5443
      'dnscrypt_config_file': './dnscrypt.yaml'
    ```

    Where `5443` is the port for your *DNSCrypt* server and `./dnscrypt.yaml` is
    the name of the configuration file generated in step 2.

[`dnscrypt`]: https://github.com/ameshkov/dnscrypt/releases



##  <a href="#generate-stamp" id="generate-stamp" name="generate-stamp">Generating A DNSCrypt Stamp</a>

Here is how to generate a *DNSCrypt* stamp and check your installation:

1.  Go to <https://dnscrypt.info/stamps/>.

1.  Enter the data from your DNSCrypt configuration file.  The *Provider public
    key* is the value of the `public_key` field in your DNSCrypt configuration
    file.  **Do not forget** to enter the host with your custom port!

1.  Now you have a stamp that looks something like this:

    ```none
    sdns://AQcAAAAAAAAADTEyNy4wLjAuMTo0NDMg8R3bzEgX5UOEX93Uy4gYSbZCJvPeOXYlZp2HuRm8T7AbMi5kbnNjcnlwdC1jZXJ0LmV4YW1wbGUub3Jn
    ```

    Check your installation by running:

    ```sh
    dnscrypt lookup-stamp\
        --domain 'example.com'\
        --stamp 'sdns://AQcAAAAAAAAADTEyNy4wLjAuMTo0NDMg8R3bzEgX5UOEX93Uy4gYSbZCJvPeOXYlZp2HuRm8T7AbMi5kbnNjcnlwdC1jZXJ0LmV4YW1wbGUub3Jn'\
        --type 'a'
    ```

    Where `example.com` is the domain name to lookup.



##  <a href="#configure-devices" id="configure-devices" name="configure-devices">Configuring Devices To Use DNSCrypt</a>

 *  **All platforms:** [`dnscrypt-proxy`][prox] (reference implementation).
    DNSCrypt-Proxy is a command-line proxy for Linux, BSD, Windows, MacOS,
    Android, and more.

 *  **Android:** [AdGuard for Android][andr] supports DNSCrypt.

 *  **iOS:** [AdGuard for iOS][ios] supports DNSCrypt.

 *  **iOS:** [DNSCloak][cloa] uses `dnscrypt-proxy` internally and supports
    DNSCrypt.

 *  **Windows:** [AdGuard for Windows][win] supports DNSCrypt.

 *  **Windows:** [Simple DNSCrypt][simp] is a simple management tool to
    configure and run `dnscrypt-proxy` on Windows.

You can find more implementations on the [DNSCrypt website][info].

[andr]: https://adguard.com/en/adguard-android/overview.html
[cloa]: https://itunes.apple.com/app/id1452162351
[info]: https://dnscrypt.info/implementations
[ios]:  https://adguard.com/en/adguard-ios/overview.html
[prox]: https://github.com/DNSCrypt/dnscrypt-proxy
[simp]: https://simplednscrypt.org/
[win]:  https://adguard.com/en/adguard-windows/overview.html
