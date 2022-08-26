 #  AdGuard Home - DHCP Server

 *  [Prerequisites](#prereq)
 *  [Default Options](#default)
 *  [Configuration](#config)
     *  [The `dhcp.dhcpv4.options` Array Field](#config-4)
     *  [DHCPv6 Options](#config-6)
 *  [Automatic Hosts](#autohosts)

AdGuard Home can be used as a DHCP server.  This page describes how to do that.



##  <a id="prereq" href="#prereq">Prerequisites</a>

1.  Make sure that you run an OS on which AdGuard Home supports DHCP.  We
    currently don't support DHCP on Windows.

1.  Make sure that your machine has a static IP address.



##  <a id="default" href="#default">Default Options</a>

By default, AdGuard Home will set itself as the DNS server for the DHCP clients.
The default lease time is 24 hours.



##  <a id="config" href="#config">Configuration</a>

See the DHCP section in the [configuration] article for the overview of the DHCP
configuration options.  There are several configuration parameters for DHCP that
can't be set via the AdGuard Home administrator dashboard.  Those are described
below.



   ###  <a id="config-4" href="#config-4">The `dhcp.dhcpv4.options` Array Field</a>

The `options` field accepts four types of values: `hex`, `ip`, `ips`, and
`text`.  They all start with the `CODE`, which MUST be an integer in the [1,
255] range.  See also [RFC 2132, sec. 3][rfc-2132].

In accordance with [RFC 2131, sec. 4.3.1][rfc-2131], these options override the
default options' values set by Adguard Home and requested by a client.  Which
means that if you want to set DNS server addresses using option `6`, you should
also add Adguard Home's own addresses there.  Otherwise, AdGuard Home's
filtering won't work for the DHCP clients who receive these DNS server
addresses.

Option `del` has been added in **v0.107.12**.  It is used to unconditionally
remove options from the server's responses, which may lead to weird behaviors.
Use with caution.  On example where this option is useful is when a client
doesn't accept a lease because of an option, see [issue 4337].

 *  The `hex` format is:

    ```
    CODE hex HEX_VALUE
    ```

    For example, to set option `6`, the DNS server, to two IP addresses,
    `1.2.3.4` and `1.2.3.5`, use:

    ```yaml
    # …
    'dhcp':
      # …
      'dhcpv4':
        # …
        'options':
        - '6 hex 0102030401020305'
    ```

 *  The `ip` format is:

    ```
    CODE ip IPV4_VALUE
    ```

    For example, to set option `6`, the DNS server, to one IP address,
    `1.2.3.4`, use:

    ```yaml
    # …
    'dhcp':
      # …
      'dhcpv4':
        # …
        'options':
        - '6 ip 1.2.3.4'
    ```

 *  The `ips` format (since **v0.106.0**) is the same, but with comma-separated
    IP-addresses:

    ```yaml
    # …
    'dhcp':
      # …
      'dhcpv4':
        # …
        'options':
        - '6 ips 1.2.3.4,5.6.7.8'
    ```

 *  The `text` format (since **v0.106.0**) allows you to put arbitrary UTF-8
    text as the option data.  For example:

    ```yaml
    # …
    'dhcp':
      # …
      'dhcpv4':
        # …
        'options':
        - '252 text http://example.com'
    ```

 *  The `del` format (since **v0.107.12**) allows you to remove an option.  For
    example:

    ```yaml
    # …
    'dhcp':
      # …
      'dhcpv4':
        # …
        'options':
        - '61 del'
    ```

[issue 4337]: https://github.com/adguardTeam/adGuardHome/issues/4337



 ###  <a id="config-6" href="#config-6">DHCPv6 Options</a>

The option `dhcp.dhcpv6.ra_slaac_only`, if `true`, sends RA packets forcing the
clients to use SLAAC.  The DHCPv6 server won't be started in this case.

The option `dhcp.dhcpv6.ra_allow_slaac`, if `true`, sends RA packets allowing
the clients to choose between SLAAC and DHCPv6.

[configuration]: https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration
[rfc-2131]:      https://tools.ietf.org/html/rfc2131#page-29
[rfc-2132]:      https://tools.ietf.org/html/rfc2132#section-3



##  <a id="autohosts" href="#autohosts">Automatic Hosts</a>

Machines in the network can be reached more easily using the hostnames they send
in the DHCP requests with a configurable top-level domain (TLD).  By default,
the TLD is `lan`.  For example, if you have a machine called “workstation” in
the network, and it sends a DHCP request with option 12 set to `workstation`,
you can reach it over HTTP on the host `http://workstation.lan`.

You can also set a custom TLD or domain name using the `dns.local_domain_name`
field in the [configuration] file.
