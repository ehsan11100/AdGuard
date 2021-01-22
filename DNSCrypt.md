 # AdGuard Home* - DNSCrypt

1. [Generating A Configuration File](#generate-config)
2. [Generating A *DNSCrypt* Stamp](#generate-stamp)
3. [Configuring Devices To Use *DNSCrypt*](#configure-devices)

<a id="generate-config"></a>

## Generating A Configuration File

Here is how to generate a *DNSCrypt* configuration file and point *AdGuardHome*
to it:

1.  Get the latest version of the [`dnscrypt`] utility for your system.
2.  Run:

    ```sh
    $ dnscrypt generate --provider-name '2.dnscrypt-cert.example.org' --out ./dnscrypt.yaml
    ```

    Where `example.org` is the name of your host and `./dnscrypt.yaml` is the
    name of the configuration output file.
3.  In your *AdGuardHome* configuration file (typically `AdGuardHome.yaml`), add
    the following lines:

    ```yaml
    'tls':
      # …
      'port_dnscrypt': 5443
      'dnscrypt_config_file': './dnscrypt.yaml'
    ```

    Where `5443` is the port for your *DNSCrypt* server and `./dnscrypt.yaml` is
    the name of the configuration file generated in step 2.

[`dnscrypt`]: https://github.com/ameshkov/dnscrypt/releases

<a id="generate-stamp"></a>

## Generating A *DNSCrypt* Stamp

Here is how to generate a *DNSCrypt* stamp and check your installation:

1.  Go to <https://dnscrypt.info/stamps/>.
2.  Enter the data from your *DNSCrypt* configuration file.  The *Provider
    public key* is the value of the `public_key` field in your *DNSCrypt*
    configuration file.  **Do not forget** to enter the host with your custom port!
3.  Now you have a stamp that looks something like this:

    ```none
    sdns://AQcAAAAAAAAADTEyNy4wLjAuMTo0NDMg8R3bzEgX5UOEX93Uy4gYSbZCJvPeOXYlZp2HuRm8T7AbMi5kbnNjcnlwdC1jZXJ0LmV4YW1wbGUub3Jn
    ```

    Check your installation by running:

    ```sh
    $ dnscrypt lookup-stamp\
        --domain 'example.com'\
        --stamp 'sdns://AQcAAAAAAAAADTEyNy4wLjAuMTo0NDMg8R3bzEgX5UOEX93Uy4gYSbZCJvPeOXYlZp2HuRm8T7AbMi5kbnNjcnlwdC1jZXJ0LmV4YW1wbGUub3Jn'\
        --type 'a'
    ```

    Where `example.com` is the domain name to lookup.

<a id="configure-devices"></a>

## Configuring Devices To Use *DNSCrypt*

- **All platforms:** [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) (reference implementation) - DNSCrypt-Proxy is a command-line proxy for Linux, BSD, Windows, MacOS, Android and more.
- **Android:** [AdGuard for Android](https://adguard.com/en/adguard-android/overview.html) supports `DNSCrypt`.
- **iOS:** [AdGuard for iOS](https://adguard.com/en/adguard-ios/overview.html) supports `DNSCrypt`.
- **iOS:** [DNSCloak](https://itunes.apple.com/app/id1452162351) uses dnscrypt-proxy internally and supports `DNSCrypt`.
- **Windows:** [AdGuard for Windows](https://adguard.com/en/adguard-windows/overview.html) supports `DNSCrypt`.
- **Windows:** [Simple DNSCrypt](https://simplednscrypt.org/) is a simple management tool to configure and run dnscrypt-proxy on Windows.

You can find more implementations on the [DNSCrypt website](https://dnscrypt.info/implementations).
