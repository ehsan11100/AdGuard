 # AdGuard Home - DHCP Server

 *  [Prerequisites](#prereq)
 *  [Default Options](#default)
 *  [Configuration](#config)
     *  [The `dhcp.dhcpv4.options` Array Field](#config-4)
     *  [DHCPv6 Options](#config-6)
 *  [Automatic Hosts](#autohosts)

*AdGuard Home* can be used as a DHCP server.  This page describes how to do that.

##  <a id="prereq" href="#prereq">Prerequisites</a>

1.  Make sure that you run an OS on which *AdGuard Home* supports DHCP.  We
    currently don't support DHCP on *Windows*.
1.  Make sure that your machine has a static IP address.

##  <a id="default" href="#default">Default Options</a>

By default, *AdGuard Home* will set itself as the DNS server for the DHCP
clients.  The default lease time is 24 hours.

##  <a id="config" href="#config">Configuration</a>

See the DHCP section in the [configuration] article for the overview of the DHCP
configuration options.  There are several configuration parameters for DHCP that
can't be set via the *AdGuard Home* administrator dashboard.  Those are
described below.

 ###  <a id="config-4" href="#config-4">The `dhcp.dhcpv4.options` Array Field</a>

The `options` field accepts two types of values: `hex` and `ip`.  Both start
with the `CODE`, which MUST be an integer in the [1, 255] range.

See [RFC 2132, sec. 3](https://tools.ietf.org/html/rfc2132#section-3).

The `hex` format is:

```
CODE hex HEX_VALUE
```

For example, to set option `6`, the DNS server, to two IP addresses, `1.2.3.4`
and `1.2.3.5`, use:

```yaml
# …
'dhcp':
  # …
  'dhcpv4':
    # …
    'options':
    - '6 hex 0102030401020305'
```

The `ip` format is:

```
CODE ip IPV4_VALUE
```

For example, to set option `6`, the DNS server, to one IP address, `1.2.3.4`,
use:

```yaml
# …
'dhcp':
  # …
  'dhcpv4':
    # …
    'options':
    - '6 ip 1.2.3.4'
```

An easier format for multiple IP addresses is planned, see issue [#2385].

 ###  <a id="config-6" href="#config-6">DHCPv6 Options</a>

The option `dhcp.dhcpv6.ra_slaac_only`, if `true`, sends RA packets forcing the
clients to use SLAAC.  The DHCPv6 server won't be started in this case.

The option `dhcp.dhcpv6.ra_allow_slaac`, if `true`, sends RA packets allowing
the clients to choose between SLAAC and DHCPv6.

[#2385]:         https://github.com/AdguardTeam/AdGuardHome/issues/2385
[configuration]: https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration

##  <a id="autohosts" href="#autohosts">Automatic Hosts</a>

You can reach machines in the network more easily using the hostnames they send
in the DHCP requests with the `.lan` top-level domain.  For example, if you have
a machine called “workstation” in the network, and it sends a DHCP request with
option 12 set to `workstation`, you can reach it over HTTP on the host
`http://workstation.lan`.

Configuring custom TLDs instead of the `.lan` one is planned, see issue [#2393].

[#2393]: https://github.com/AdguardTeam/AdGuardHome/issues/2393
