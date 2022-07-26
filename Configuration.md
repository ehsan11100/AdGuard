 #  AdGuard Home - Configuration

Most of these settings can be changed via the web-based admin interface.
However, we decided to list them all here just in case.

 *  [Command-line Arguments](#command-line)
 *  [Configuring Upstreams](#upstreams)
     *  [Specifying Upstreams For Domains](#upstreams-for-domains)
     *  [Loading Upstreams From File](#upstreams-from-file)
     *  [Specifying Upstreams For Reverse DNS](#upstreams-for-rdns)
         *  [Private Addresses](#rdns-private)
         *  [Public Addresses](#rdns-public)
         *  [Client Look-Ups](#rdns-clients)
 *  [Configuration File](#configuration-file)
 *  [Reset Web Password](#password-reset)
 *  [Profiling With Pprof](#pprof)



##  <a href="#command-line" id="command-line" name="command-line">Command-line Arguments</a>

Here is a list of all available command-line arguments.

```
$ ./AdGuardHome --help
Usage:

./AdGuardHome [options]

Options:
  -c, --config VALUE                 Path to the config file.
  -w, --work-dir VALUE               Path to the working directory.
  -h, --host VALUE                   Host address to bind HTTP server on.
  -p, --port VALUE                   Port to serve HTTP pages on.
  -s, --service VALUE                Service control action: status, install, uninstall, start, stop, restart, reload (configuration).
  -l, --logfile VALUE                Path to log file.  If empty: write to stdout; if 'syslog': write to system log.
  --pidfile VALUE                    Path to a file where PID is stored.
  --check-config                     Check configuration and exit.
  --no-check-update                  Don't check for updates.
  --no-mem-optimization              Deprecated.  Disable memory optimization.
  --no-etc-hosts                     Deprecated.  Do not use the OS-provided hosts.
  --local-frontend                   Use local frontend directories.
  -v, --verbose                      Enable verbose output.
  --glinet                           Run in GL-Inet compatibility mode.
  --version                          Show the version and exit.  Show more detailed version description with -v.
  --help                             Print this help.
```

Please note, that the command-line arguments override settings from the configuration file.

`./AdGuardHome -s reload` command does the following:

 -  refresh the runtime clients data from the operating system's ARP tables;

 -  re-read SSL certificate file (if it has changed).

Command-line arguments passed to `-s install` command will be then used by the
service.  For instance, if you install the AdGuard Home service using `sudo
./AdGuardHome -s install --no-check-update`, it will disable update check
functionality in the service as well.



##  <a href="#upstreams" id="upstreams" name="upstreams">Configuring Upstreams</a>

AdGuard Home is basically a DNS proxy that sends your DNS queries to the
upstream servers.  You can specify multiple upstream servers in AdGuard Home
settings, or you can even specify a DNS server that will be used to resolve
specific domains.

Examples:

* `94.140.14.140`: plain DNS (over UDP).

* `tls://dns-unfiltered.adguard.com`: encrypted [DNS-over-TLS].

* `https://cloudflare-dns.com/dns-query`: encrypted [DNS-over-HTTPS].

* `quic://dns-unfiltered.adguard.com:853`: encrypted [DNS-over-QUIC].

* `tcp://1.1.1.1`: plain DNS (over TCP).

* `sdns://...`: [DNS Stamps] for [DNSCrypt] or [DNS-over-HTTPS] resolvers.

* `[/example.local/]1.1.1.1`: DNS upstream for specific domains, see below.

* `[/*.example.local/]1.1.1.1`: DNS upstream for specific subdomains, see below.

###  <a href="#upstreams-for-domains" id="upstreams-for-domains" name="upstreams-for-domains">Specifying Upstreams For Domains</a>

You can specify upstreams that will be used for specific domains using the
dnsmasq-like syntax (see the documentation for the option `--server`
[here][dnsmasq-man]).  This feature is intended for private nameservers which
deal with intranet domains.

The syntax is:

```none
[/domain1/domain2/domainN/]upstreamString
```

If one or more domains are specified, that upstream (here `upstreamString`) is
used only for those domains.  Usually, it is used for private nameservers.  For
instance, if you have a nameserver on your network which deals with
`xxx.internal.local` at address `192.168.0.1` then you can specify
`[/internal.local/]192.168.0.1`, and AdGuard Home will send all
`*.internal.local` queries to that nameserver.  Everything else will be sent to
the default upstreams, which should be specified as well.

An empty domain specification, `//` has the special meaning of “unqualified
names only”, i.e. names without any dots in them, like `myhost` or `router`.

More specific domains take precedence over less specific domains.  So,
a configuration like this:

```none
[/host.com/]1.2.3.4
[/www.host.com/]2.3.4.5
```

sends queries for `*.host.com` to `1.2.3.4`, except for queries for
`*.www.host.com`, which are sent to `2.3.4.5`.

The special server address `#` means “use the default servers”. So,
a configuration like this:

```none
6.7.8.9
[/host.com/]1.2.3.4
[/www.host.com/]#
```

sends queries for `*.host.com` to `1.2.3.4` except for queries for
`*.www.host.com`, which are sent to `6.7.8.9`, which is the default upstream.

Since **v0.108.0-b.8** the wildcard `*` has a special meaning of "any subdomain", 
so `--upstream=[/*.host.com/]1.2.3.4` will send queries for `*.host.com` to `1.2.3.4`, 
but `host.com` will be forwarded to default upstreams.

  ####  Examples

 *  A configuration like:

    ```none
    8.8.8.8:53
    [/local/]192.168.0.1:53
    ```

    sends queries for `*.local` domains to `192.168.0.1:53`.  Other queries are
    sent to `8.8.8.8:53`.

 *  A configuration like:

    ```none
    8.8.8.8:53
    [/host.com/]1.1.1.1:53
    [/maps.host.com/]#
    ```

    sends queries for `*.host.com` to `1.1.1.1:53` except for `*.maps.host.com`
    which are sent to `8.8.8.8:53` along with all other queries.
    
 *  A configuration like:

    ```none
    8.8.8.8:53
    [/host.com/]1.1.1.1:53
    [/*.host.com/]2.2.2.2:53
    ```

    sends queries for `*.host.com` to `2.2.2.2:53` except for `host.com` queries
    those are sent to `1.1.1.1:53`, but all other queries are sent to `8.8.8.8:53`.

   ###  <a href="#upstreams-from-file" id="upstreams-from-file" name="upstreams-from-file">Loading Upstreams From File</a>

Using specific upstreams for some domains is a common way to accelerate internet
in China.  For an example, see https://github.com/felixonmars/dnsmasq-china-list
or any other of the many `dnsmasq` lists.  These lists can be easily converted
to a list for AdGuard Home:

```none
Before:  server=/0-100.com/114.114.114.114
After:   [/0-100.com/]114.114.114.114
```

The problem with these lists is that they may be too large.  In this case you
may want to load them from a separate file instead of setting all upstreams in
AdGuard Home settings.  To do that, simply specify the path to a file with your
list in the `upstream_dns_file` field of `AdGuardHome.yaml`.

**BUG:** The file, just like the input in the web interface, currently doesn't
accept internationalized domain names (e.g. `пример.рф` or `例子.中国`).  As
a workaround, convert them to Punycode (e.g. `xn--e1afmkfd.xn--p1ai` or
`xn--fsqu00a.xn--fiqs8s` respectively).  See [issue 2915][2915].

   ###  <a href="#upstreams-for-rdns" id="upstreams-for-rdns" name="upstreams-for-rdns">Specifying Upstreams For Reverse DNS</a>

Using the domain-specific upstream notation, you can specify dedicated upstream
DNS servers for reverse DNS (rDNS) requests.  If you want **all** your `PTR`
queries to be redirected to `192.168.8.8`:

1.  Enter the following into the “Upstream DNS servers” field on the “Settings
    → DNS settings” page:

    ```none
    [/in-addr.arpa/]192.168.8.8
    [/ip6.arpa/]192.168.8.8
    ```

2.  Enter the following into the “Private reverse DNS servers” field on the
    same page below the previous field:

    ```none
    192.168.8.8
    ```

    There is no need to use the domain-specific notation here, unless you want
    to redirect requests for different private ranges to different upstream
    servers.

**NOTE:**  All upstreams for private ranges **must** go to the “Private reverse
DNS servers” field **and not** the main “Upstream DNS servers” field.  Entering
something like `[/192.in-addr.arpa/]192.168.8.8` into the main field will have
no effect.

Read below for more details.

  ####  <a href="#rdns-private" id="rdns-private" name="rdns-private">Private Addresses</a>

Since **v0.106.0** all the addresses from [private IP ranges][private-ip] are
only resolved via appropriate local resolvers to avoid leaks of clients'
information.  By default, AdGuard Home tries to get the addresses of the default
resolvers from the OS.  You can set custom upstreams for it in the “Private
reverse DNS servers” field in the “Upstream DNS servers” section or via the
`local_ptr_upstreams` field in the configuration file.  Since **v0.108.0-b.5**
the private IP ranges may be customized through the `private_networks` field.
It's empty by default which makes AdGuard Home use the aforementioned default
set of networks.

Since **v0.107.0** you can disable the usage of private reverse DNS upstream
servers via the “Use private reverse DNS resolvers” checkbox in the “Upstream
DNS servers” section or via the `use_private_ptr_resolvers` field in the
configuration file.  If it is disabled, the unknown addresses from locally
served networks won't be resolved at all, and clients performing these queries
will receive `NXDOMAIN` responses.

  ####  <a href="#rdns-public" id="rdns-public" name="rdns-public">Public Addresses</a>

If you want AdGuard Home to use another DNS server for a specific IP address
range, you can do it using the same syntax as for general upstream servers.  For
example, if you add this to your “Upstream DNS servers” field:

```none
[/200.in-addr.arpa/]192.168.7.7
```

then AdGuard Home will use the `192.168.7.7` DNS server for all rDNS requests to
resolve clients' IP addresses from the `200.0.0.0/8` network.

Note that if you want to use that address for `PTR` queries for IP addresses in
a locally served network range, for example `192.168.0.0/16`, you should add
this to the “Private reverse DNS servers” field:

```none
[/168.192.in-addr.arpa/]192.168.7.7
```

  ####  <a href="#rdns-clients" id="rdns-clients" name="rdns-clients">Client Look-Ups</a>

AdGuard Home automatically gets the names of connected devices using reverse DNS
lookup (rDNS).  It sends `PTR` requests with the IP addresses of clients to
appropriate DNS servers and uses the responses to enrich client information with
human-friendly names.

Since **v0.106.0** you can enable and disable this feature with “Enable clients'
hostname resolution” setting in the “Upstream DNS servers” section or via the
`resolve_clients` field in the configuration file.

[2915]:           https://github.com/AdguardTeam/AdGuardHome/issues/2915
[DNS Stamps]:     https://dnscrypt.info/stamps/
[DNS-over-HTTPS]: https://en.wikipedia.org/wiki/DNS_over_HTTPS
[DNS-over-QUIC]:  https://datatracker.ietf.org/doc/html/rfc9250
[DNS-over-TLS]:   https://en.wikipedia.org/wiki/DNS_over_TLS
[DNSCrypt]:       https://dnscrypt.info/
[dnsmasq-man]:    http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html
[private-ip]:     https://tools.ietf.org/html/rfc6303



##  <a href="#configuration-file" id="configuration-file" name="configuration-file">Configuration File</a>

Upon the first execution, a file named `AdGuardHome.yaml` will be created, with default values written in it. You can modify the file while your AdGuard Home service is not running. Otherwise, any changes to the file will be lost because the running program will overwrite them.

Settings are stored in [YAML format](https://en.wikipedia.org/wiki/YAML), possible parameters that you can configure are listed below:

- `bind_host` — Web interface IP address to listen on.
- `bind_port` — Web interface IP port to listen on.
- `users` — Web users info
  - `name` — User name
  - `password` — BCrypt-encrypted password
- `auth_attempts` (**after v0.106.0**) – Maximum number of failed login attempts
  a user can do before being blocked.  The entire blocking logic is disabled if
  it equals to 0.
- `block_auth_min` (**after v0.106.0**) – The duration of blocking period.  The
  entire blocking logic is disabled if it equals to 0.
- `http_proxy` — Proxy URL for HTTP client (e.g. "http://user:password@server:port/"). Supports "http", "https" and "socks5" scheme.
- `web_session_ttl` — Web session TTL (in hours) - a web user will stay signed in for this amount of time.
- `debug_pprof` — Enable pprof HTTP server listening on port 6060 for debugging. See section `Profiling with pprof`.

- `dns` — DNS configuration section.
  - **General settings**
    - `bind_host` (**before v0.106.0**) - IP address on which to serve DNS
       queries.
    - `bind_hosts` (**after v0.106.0**) - IP addresses on which to serve DNS
       queries.  For each network interface there can only be one IP address of
       each IP version.  Examples of valid configurations:

       ```yaml
       # Different network interfaces.
       'dns':
           'bind_hosts':
           - '127.0.0.1'
           - '192.168.1.1'
       ```

       ```yaml
       # Same network interface, different IP versions.
       'dns':
           'bind_hosts':
           - '127.0.0.1'
           - '::1'
       ```

       If you want your server to accept requests on all interfaces and using
       both IP versions, for example if you run a public server, put **one**
       item with the unspecified IP of any version:

       ```yaml
       'dns':
           'bind_hosts':
           - '0.0.0.0'
       ```
    - `port` — DNS server port to listen on.
    - `statistics_interval` — Time interval for statistics (in days)
  - **Protection settings**
    - `protection_enabled` — Whether any kind of filtering and protection should
      be performed.  **Since v0.107.0** it doesn't affect the rules with
      `$dnsrewrite` modifier and other rewrites, including those taken from the
      operating system's hosts file.
    - `filtering_enabled` — Whether filtering of DNS requests based on rule
      lists should be performed.
    - `blocking_mode` — Specifies how to block DNS requests. "nxdomain" (default): respond with NXDOMAIN status; "null_ip": respond with the unspecified IP address (0.0.0.0); or "custom_ip": respond with `blocking_ipv4` or `blocking_ipv6`.
    - `blocking_ipv4` - IP address to be returned for a blocked A request if `blocking_mode` is set to `custom_ip`
    - `blocking_ipv6` - IP address to be returned for a blocked AAAA request if `blocking_mode` is set to `custom_ip`
    - `blocked_response_ttl` — For how many seconds the clients should cache a filtered response. Low values are useful on LAN if you change filters very often, high values are useful to increase performance and save traffic.
    - `parental_block_host` — IP (or domain name) which is used to respond to DNS requests blocked by parental control
    - `safebrowsing_block_host` — IP (or domain name) which is used to respond to DNS requests blocked by safe-browsing
    - `parental_enabled` — Parental control-based DNS requests filtering.
    - `safesearch_enabled` — Enforcing "Safe search" option for search engines, when possible.
    - `safebrowsing_enabled` — Filtering of DNS requests based on safebrowsing.
  - **Query log settings**
    - `querylog_enabled` — Query logging (also used to calculate top 50 clients, blocked domains and requested domains for statistical purposes).
    - `querylog_file_enabled` — Write query logs to a file.
    - `querylog_interval` - time interval for query log files rotation.  It's in
      days **until v0.107.0**, the possible values are: `90`, `30`, `7`, `1`.
      **Since v0.107.0** it's a human-readable duration in hours, the possible
      values are: `2160h`, `720h`, `168h`, `24h`, `6h`.
    - `querylog_size_memory` - Number of entries kept in memory before they are flushed to disk
    - `anonymize_client_ip` - If true, anonymize clients' IP addresses in logs and stats
  - **Anti-DNS amplification features**
    - `ratelimit` — DDoS protection, specifies in how many packets per second a client should receive. Anything above that is silently dropped. To disable set 0, default is 20. Safe to disable if DNS server is not available from internet.
    - `ratelimit_whitelist` — If you want exclude some IP addresses from ratelimiting but keep ratelimiting on for others, put them here.
    - `refuse_any` — Another DDoS protection mechanism. Requests of type ANY are rarely needed, so refusing to serve them mitigates against attackers trying to use your DNS as a reflection. Safe to disable if DNS server is not available from internet.
  - **Upstream DNS servers settings**
    - `upstream_dns` — List of upstream DNS servers.
    - `upstream_dns_file` — Path to a file with the list of upstream DNS servers. If it is configured, the value of `upstream_dns` is ignored.
    - `bootstrap_dns` — List of DNS servers used for initial hostname resolution in case an upstream server name is a hostname.
    - `private_networks` (**since v0.108.0-b.5**) — List of networks used to
      check if an IP address belongs to a locally-served address registry.  If
      empty, AdGuard Home will use the set defined by [RFC 6303][private-ip].
    - `use_private_ptr_resolvers` (**since v0.107.0**) – If AdGuard Home should
      use private reverse DNS servers.
    - `local_ptr_upstreams` (**since v0.106.0**) – List of upstream DNS servers
      to resolve PTR requests for addresses inside locally-served networks. If
      empty, AdGuard Home will automatically try to get local resolvers from the
      OS.  **Since v0.108.0-b.4** the domain-specific upstreams are validated
      for being a valid ARPA domain pointing to a locally-served network.
    - `all_servers` — Enables parallel queries to all configured upstream
      servers to speed up resolving.

      If enabled, the queries are sent to each server simultaneously and the
      first response is chosen.
    
      If disabled, the queries are sent to each upstream server one-by-one and
      then sorted by RTT.  Note that more stable upstream servers are preferred
      by the algorithm.
    - `fastest_addr` — Use the Fastest Address algorithm.  It finds an IP
      address with the lowest latency and returns this IP address in DNS
      response.
    - `fastest_timeout` (**since v0.107.0**) — The timeout used for dialing the
      addresses while picking the fastest.  Values other than positive ones are
      replaced with the default one, `1s`.
  - **ECS settings**
    - `edns_client_subnet` — Add EDNS Client Subnet (ECS) option to upstream
      requests and log the values sent by the clients in the query log.  Please
      note that ECS option will be added automatically for clients with public
      IP addresses only.
  - **Access settings**
    - `allowed_clients` — IP addresses of allowed clients
    - `disallowed_clients` — IP addresses of clients that should be blocked
    - `blocked_hosts` — Hosts that should be blocked
    - `trusted_proxies` (**since v0.107.0**) – The list of IP addresses and CIDR
      prefixes of trusted HTTP proxy servers.  If a DNS-over-HTTPS request comes
      from one of these addresses or networks, AdGuard Home uses the provided
      proxy headers, such as `X-Real-IP`, to get the real IP address of the
      client.  Requests from HTTP proxies outside of these networks are
      considered to be requests from the proxy itself.  That is, the proxy
      headers are ignored.

      The full list of proxy headers, in the order AdGuard Home inspects them:
      1.  `CF-Connecting-IP`
      1.  `True-Client-IP`
      1.  `X-Real-IP`
      1.  `X-Forwarded-For`
  - **DNS cache settings**
    - `cache_size` — DNS cache size (in bytes).
    - `cache_ttl_min` — The minimum TTL override, in seconds.  If the TTL of
      a response from upstream is below this value, the TTL is replaced with it.
      Must be less than or equal to `cache_ttl_max`.
    - `cache_ttl_max` — The maximum TTL override, in seconds.  If the TTL of
      a response from upstream is above this value, the TTL is replaced with it.
      Must be greater than or equal to `cache_ttl_min`.
    - `cache_optimistic` (**since v0.107.0**) — Make AdGuard Home respond from
      the cache even when the entries are expired and also try to refresh them.
      Before **v0.108.0-b.5** the TTL for such responses is 60 seconds and since
      **v0.108.0-b.5** it's 10 seconds.
  - **Other settings**
    - `bogus_nxdomain` - Respond with `NXDOMAIN` instead of any response having
      IP addresses matching the ones from this list.  **Since v0.108.0** it also
      supports CIDRs.
    - `enable_dnssec` - Set DNSSEC flag in the outgoing DNS requests and check the result
    - `aaaa_disabled` — Respond with an empty answer to all AAAA requests
    - `safebrowsing_cache_size` — Safe Browsing cache size (in bytes)
    - `safesearch_cache_size` — Safe Search cache size (in bytes)
    - `parental_cache_size` — Parental Control cache size (in bytes)
    - `cache_time` — Safe Browsing, Safe Search, Parental Control cache TTL
    - `max_goroutines` — Max. number of parallel goroutines for processing incoming requests
    - `handle_ddr` — Handle [Discovery of Designated Resolvers (DDR)][DDR] requests.
    - `ipset` — List of domain-ipset_name associations for adding IP addresses of the specified domain names to an ipset list. Syntax: `DOMAIN[,DOMAIN].../IPSET_NAME[,IPSET_NAME]...`. IPv4 addresses are added to an ipset list with `ipv4` family, IPv6 addresses - to `ipv6` ipset list. ipset list must exist. This setting is supported on Linux only. This feature is similar to `--ipset` in dnsmasq.
    - `upstream_timeout` (**since v0.107.0**) — The timeout for querying
      upstream servers.  Zero value will be rewritten with default one which is
      10s.
    - `resolve_clients` (**since v0.106.0**) - Enable/disable resolving clients'
      addresses by sending PTR requests.
- `filters` — List of filters, each filter has the following values:
  - `enabled` — Current filter's status (enabled/disabled).
  - `url` — URL pointing to the filter contents (filtering rules).
  - `name` — Name of the filter. If it's an adguard syntax filter it will get updated automatically, otherwise it stays unchanged.
  - `last_updated` — Time when the filter was last updated from server.
  - `ID` - filter ID (must be unique).
- `dhcp` - Built-in DHCP server configuration.  See also the [DHCP] article.
  - `enabled` - DHCP server status.
  - `interface_name` - network interface name (`eth0`, `en0`, and so on).
  - `dhcpv4` - DHCPv4 settings.
    - `gateway_ip` - gateway IP address.
    - `subnet_mask` - subnet mask.
    - `range_start`, `range_end` - the start and the end of the leased IP
      address range.
    - `lease_duration` - lease duration in seconds.  If `0`, use the default
      duration of 24 hours.
    - `options` - custom DHCP options.  See the [DHCP] article section on these
      options for more information.
  - `dhcpv6` - DHCPv6 settings.
    - `range_start` - the first IP address to be assigned to a client.
    - `lease_duration` - same as in v4 above.
    - `ra_slaac_only` and `ra_allow_slaac` - send RA packets either forcing the
      clients to use SLAAC or allowing them to choose.  See the [DHCP] article
      section on these options for more information.
  - `local_domain_name` — The domain name that AdGuard Home's DHCP server uses
    for hostnames of its clients.  The default value, which is also set when
    this value is empty, is `lan`.  So, if you have a machine called `myhost` in
    your network, and AdGuard Home is this network's DHCP server, the hostname
    of that machine is `myhost.lan`.

    DNS queries of type `A` for such hosts are only allowed from locally served
    networks, such as `10.0.0.0/8`, `192.168.0.0/16`, and so on.  Other clients
    receive an empty `NXDOMAIN` response.

    **Before v0.108.0** this setting was a part of the `dns` object.
- `tls` - HTTPS/DOH/DOT settings.
  - `enabled` - encryption (DOT/DOH/HTTPS) status.
  - `server_name` - The hostname of your server.  If set, it is used to detect
    ClientIDs (using the ServerName field of ClientHello messages), respond to
    [Discovery of Designated Resolvers (DDR)][DDR] queries, and perform
    additional connection validations.  If not set, these features are disabled.

    Must match one of the DNS Names in the certificate.
  - `force_https` - if true, forces HTTP->HTTPS redirect.
  - `port_https` - HTTPS port.  If `0`, HTTPS is disabled.
  - `port_dns_over_tls` - DNS-over-TLS port.  If `0`, DNS-over-TLS is disabled.
  - `port_dns_over_quic` - DNS-over-QUIC port.  If `0`, DNS-over-QUIC is
    disabled.  Before **v0.108.0-b.5** the default value was `754`, since
    **v0.108.0-b.5** it's `853`.
  - `port_dnscrypt` - DNSCrypt port.  If `0`, DNSCrypt is disabled.  See
    [DNSCrypt] for more information and examples.
  - `dnscrypt_config_file` - DNSCrypt configuration file path.  **Must** be set
    if `port_dnscrypt` is not `0`.  See the [`dnscrypt`] utility documentation
    for examples of configuration generation.
  - `allow_unencrypted_doh` - Allow DOH queries via unencrypted HTTP (e.g. for reverse proxying)
  - `certificate_chain` - PEM-encoded certificates chain.
  - `strict_sni_check` - Reject connection if the client uses server name (in SNI) that doesn't match the certificate
  - `private_key` - PEM-encoded private key.
  - `icmp_timeout_msec` - time (ms) to wait for ICMP reply to detect an IP conflict. If 0, the feature is disabled.
- `user_rules` — User-specified filtering rules.
- `os` (**since v0.107.0**) — Operating system related settings.
  - `group` — The name of the user group to switch to after the startup.
  - `user` — The name of the user to switch to after the startup.
  - `rlimit_nofile` — Limit on the maximum number of open files for the server
    process (on unixlike OSes).  Set to `0` to use the system's default value.
- **Clients settings**
  - `clients` — Persistent and runtime clients settings.
    - `persistent` — An array of explicitly configured clients.  **Before
      v0.108.0** the contents of this field took up the whole `clients` section.
    - `runtime_sources` — **Since v0.108.0** this controls the certain runtime
      clients sources.
      - `whois` — Request WHOIS information for clients with public IP
        addresses.
      - `arp` — Consider the operating system's ARP table.
      - `rdns` — Perform rDNS lookups for client's address.
      - `dhcp` — Check AdGuard Home's DHCP leases for client's address.
      - `hosts` — Follow the operating system's hosts files.
- **Log settings**
  - `log_file` — Path to the log file. If empty, writes to stdout, if `syslog` -- system log (or eventlog on Windows).
  - `log_compress` — Compress determines if the rotated log files should be compressed using gzip (default: false)
  - `log_localtime` — If the time used for formatting the timestamps in is the computer's local time (default: false [UTC])
  - `log_max_backups` — Maximum number of old log files to retain (MaxAge may still cause them to get deleted) (default: 0, which retains all old log files)
  - `log_max_size` — Maximum size in megabytes of the log file before it gets rotated (default: 100 MB)
  - `log_max_age` — MaxAge is the maximum number of days to retain old log files (default: 3)
  - `verbose` — Enable our disables debug verbose output (default: false)

Removing an entry from settings file will reset it to the default value. Deleting the file will reset all settings to the default values.

[DHCP]: https://github.com/AdguardTeam/AdGuardHome/wiki/DHCP
[DNSCrypt]: https://github.com/AdguardTeam/AdGuardHome/wiki/DNSCrypt
[`dnscrypt`]: https://github.com/ameshkov/dnscrypt
[DDR]: https://www.ietf.org/archive/id/draft-ietf-add-ddr-06.html



##  <a href="#password-reset" id="password-reset" name="password-reset">Reset Web Password</a>

Please follow these steps to create a new password for your user account:

1. Install `htpasswd`, which is a part of *Apache2 Web Server*:

    Ubuntu:

    `sudo apt-get install apache2`

    Fedora:

    `sudo dnf install apache2`

    Windows:

    > Choose a download from https://httpd.apache.org/docs/current/platform/windows.html#down, extract the downloaded folder, open a terminal, navigate to its `bin` folder with the `cd` command, and run `.\Htpasswd` (Note the capital H in the Windows version).

    Other versions of `htpasswd` could be used, but **only** if they support *bcrypt* hash encryption, which rules out e.g. most web-hosted `htpasswd` generators.

2. Use the `htpasswd` utility to generate a new hash:

    Ubuntu/Fedora:

    `htpasswd -B -n -b <USERNAME> <PASSWORD>`

    Windows:

    `.\Htpasswd -B -n -b <USERNAME> <PASSWORD>`

    It will print `<USERNAME>:<HASH>` to the terminal.

3. Open `AdGuardHome.yaml` in a text editor with sudo rights.

    In the `users:` section, find your username and insert the `<HASH>` value for the `password` setting:

    ```users:
    - name: ...
      password: <HASH>```

4. Save the file, restart AGH. Now you'll be able to log in to the Web interface using your new password.



##  <a href="#pprof" id="pprof" name="pprof">Profiling With Pprof</a>

To enable pprof, set `debug_pprof: true` in yaml configuration file and then
restart AdGuard Home.  Now you can get profiling information with your browser,
for example `http://localhost:6060/debug/pprof/goroutine?debug=2` will show the
call trace of each running goroutine.

This URL lets you see information about the heap usage of the AdGuard Home
process: `http://localhost:6060/debug/pprof/heap?debug=1`.

Or, with `go tool pprof`:

```sh
go tool pprof -top http://localhost:6060/debug/pprof/heap
```

For a list of supported profiles go to `http://localhost:6060/debug/pprof/`.

Alternatively, you may want to simply download the file and analyze it later:

```sh
wget http://localhost:6060/debug/pprof/heap
go tool --http=':8080' heap
```
