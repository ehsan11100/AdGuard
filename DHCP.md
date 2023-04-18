 #  AdGuard Home - DHCP server

 *  [Prerequisites](#prereq)
 *  [Default options](#default)
 *  [Configuration](#config)
     *  [The `dhcp.dhcpv4.options` array field](#config-4)
     *  [DHCPv6 options](#config-6)
 *  [Automatic hosts](#autohosts)
 *  [Stored leases](#storedleases)

AdGuard Home can be used as a DHCP server.  This page describes how to do that.



##  <a id="prereq" href="#prereq">Prerequisites</a>

1.  Make sure that you run an OS on which AdGuard Home supports DHCP.  We
    currently don't support DHCP on Windows.

1.  Make sure that your machine has a static IP address.



##  <a id="default" href="#default">Default options</a>

By default, AdGuard Home will set itself as the DNS server for the DHCP clients.
The default lease time is 24 hours.



##  <a id="config" href="#config">Configuration</a>

See the DHCP section in the [configuration] article for the overview of the DHCP
configuration options.  There are several configuration parameters for DHCP that
can't be set via the AdGuard Home administrator dashboard.  Those are described
below.



   ###  <a id="config-4" href="#config-4">The `dhcp.dhcpv4.options` array field</a>

The `options` field is used to explicitly specify the values for DHCP options
and modify the response.  In accordance with *Section 4.3.1* of [RFC
2131][rfc-2131], these options override the default options' values set by
Adguard Home and requested by a client.  Which means that if you want to set
custom DNS server addresses using option `6` (Domain Name Server), you may want
also add Adguard Home's own addresses there.  Otherwise, AdGuard Home's
filtering won't work for the DHCP clients who receive these DNS server
addresses.

Any option begins with an option *code* written as decimal integer.  See [RFC
2132][rfc-2132] for the actual DHCP option codes and allowed lengths.  The code
is followed by an option's *type* and *value*.  Currently the following *types*
are supported:

 *  `bool` (since **v0.107.12**) accepts a human-readable form of a boolean
    value, and has the length of 1 octet.

    For example:

    ```yaml
    'options':
    - '19 bool 0'     # Disable IP forwarding for hosts.
    - '20 bool t'     # Enable non-local source routing for hosts.
    - '27 bool F'     # Disable ahoming for hosts.
    - '30 bool true'  # Enable mask supplying for supporting hosts.
    - '36 bool False' # Make the hosts use RFC 894 for ethernet encapsulation.
    ```

 *  `del` (since **v0.107.12**) is a no-value option and is used to
    unconditionally remove options from the server's responses (which may lead
    to weird behaviors, use with caution).

    Since the list of options is interpreted sequentially from first to last,
    the subsequent option may override the previous ones.  So this:

    ```yaml
    'options':
    - '19 bool T'
    - '19 del'
    - '20 del'
    - '20 bool F'
    ```

    instructs to remove the option `19`, and to set the option `20` to `false`.

 *  `dur` (since **v0.107.12**) accepts a human-readable form of a duration in
    range [0 – 4294967296 seconds (about 136 days)] and has a length of *4*
    octets, just like a 32-bit unsigned integer.

    Here is the example of setting the MTU aging timeout to 10 minutes:

    ```yaml
    'options':
    - '24 dur 10m'
    ```

 *  `hex` accepts a sequence of hexadecimal numbers of an arbitrary length.

    Here is the example of setting the plateau table for path MTU timeouts:

    ```yaml
    'options':
    - '25 hex 0044012801FC03EE05D407D211001FE645FA'
    ```

 *  `ip` accepts an IPv4 address and has a length of *4* octets, just like an
    IPv4 itself.

    Here is the example of setting a broadcast address option:

    ```yaml
    'options':
    - '28 ip 192.168.0.255'
    ```

 *  `ips` (since **v0.106.0**) accepts a comma-separated list if IPv4 addresses.
    It has an arbitrary length, but is always a multiple of *4* octets.

    Here is the example of setting the domain name servers to `1.2.3.4` and
    `1.2.3.5`:

    ```yaml
    'options':
    - '6 ips 1.2.3.4,1.2.3.5'
    ```

 *  `text` (since **v0.106.0**) accepts an arbitrary UTF-8 encoded string and
    has a length of encoded text.

    Here is the example of setting the path to configuration file for WPAD:

    ```yaml
    'options':
    - '252 text http://server.domain/proxyconfig.pac'
    ```

 *  `u8` (since **v0.107.12**) accepts a decimal number in range [0 – 255] and
    takes *1* octet, just like an unsigned 8-bit integer.

    Here is the example of setting the TTL for Internet Protocol:

    ```yaml
    'options':
    - '23 u8 64'
    ```

 *  `u16` (since **v0.107.12**) accepts a decimal number in range [0 – 65535]
    and takes *2* octets, just like an unsigned 16-bit integer.

    Here is the example of setting the maximum datagram reassembly size to 576
    bytes:

    ```yaml
    'options':
    - '22 u16 576'
    ```

**NOTE:** Thoroughly check that the option format and value are valid for the
chosen type in accordance with [RFC 2132][rfc-2132] or others. AdGuard Home does
not perform any option-specific validations.

Currently there is a set of options listed in *Appendix A* of [RFC
2131][rfc-2131] with the default values chosen according to the documents
mentioned there:

| Option                           | Value                                         |
| -------------------------------- | --------------------------------------------- |
| IP Forwarding                    | Disabled                                      |
| Non-Local Source Routing         | Disabled                                      |
| Maximum Datagram Reassembly Size | 576 bytes                                     |
| Default IP Time-to-live          | 64 seconds                                    |
| Path MTU Aging Timeout Option    | 10 minutes                                    |
| Path MTU Plateau Table           | See [Table 7.1 in RFC 1191][rfc-1191-tbl-7.1] |
| Interface MTU                    | 576 bytes                                     |
| All subnets are local            | False                                         |
| Perform Mask Discovery           | False                                         |
| Mask Supplier                    | False                                         |
| Perform Router Discovery         | True                                          |
| Router Solicitation Address      | 224.0.0.2                                     |
| Broadcast Address                | 255.255.255.255                               |
| Use Trailer Encapsulation        | False                                         |
| ARP Cache Timeout                | 1 minute                                      |
| Ethernet Encapsulation version   | RFC 894                                       |
| Default TCP TTL                  | 60 seconds                                    |
| TCP Keepalive Interval           | 2 hours                                       |
| Put TCP Keepalive Garbage        | True                                          |
| Routers                          | `gateway_ip` from configuration               |
| Subnet Mask                      | `subnet_mask` from configuration              |

Some of these values may appear obsolete or may cause issues with some DHCP
client implementations among the many existing.  In accordance with [RFC
2131][rfc-2131] the options, when not explicitly configured, are only returned
if requested by client within the option `55` (Parameter Request List).


 ###  <a id="config-6" href="#config-6">DHCPv6 options</a>

The option `dhcp.dhcpv6.ra_slaac_only`, if `true`, sends RA packets forcing the
clients to use SLAAC.  The DHCPv6 server won't be started in this case.

The option `dhcp.dhcpv6.ra_allow_slaac`, if `true`, sends RA packets allowing
the clients to choose between SLAAC and DHCPv6.

[configuration]:    https://github.com/AdguardTeam/AdGuardHome/wiki/Configuration
[rfc-1191-tbl-7.1]: https://datatracker.ietf.org/doc/html/rfc1191#section-7.1
[rfc-2131]:         https://datatracker.ietf.org/doc/html/rfc2131
[rfc-2132]:         https://datatracker.ietf.org/doc/html/rfc2132



##  <a id="autohosts" href="#autohosts">Automatic hosts</a>

Machines in the network can be reached more easily using the hostnames they send
in the DHCP requests with a configurable top-level domain (TLD).  By default,
the TLD is `lan`.  For example, if you have a machine called “workstation” in
the network, and it sends a DHCP request with option 12 set to `workstation`,
you can reach it over HTTP on the host `http://workstation.lan`.

You can also set a custom TLD or domain name using the `dns.local_domain_name`
field in the [configuration] file.



##  <a id="storedleases" href="#storedleases">Stored leases</a>

DHCP leases stored in `data/leases.json`.  The file format is not stable and
may change in the future releases.
