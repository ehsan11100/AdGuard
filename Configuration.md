# AdGuard Home - Configuration

Most of these settings can be changed via the web-based admin interface. However, we decided to list them all here just in case.

- [Command-line arguments](#command-line)
- [Configuring upstreams](#upstreams)
  - [Specifying upstreams for domains](#upstreams-for-domains)
  - [Loading the list of upstream servers from a file](#upstreams-from-file)
  - [Specifying upstreams for rDNS](#upstreams-for-rdns)
- [Configuration file](#configuration-file)
- [Reset Web Password](#password-reset)
- [Profiling with pprof](#pprof)

<a id="command-line"></a>

## Command-line arguments

Here is a list of all available command-line arguments.

```
$ ./AdGuardHome --help
Usage:

./AdGuardHome [options]

Options:
  -c, --config VALUE                 Path to the config file
  -w, --work-dir VALUE               Path to the working directory
  -h, --host VALUE                   Host address to bind HTTP server on
  -p, --port VALUE                   Port to serve HTTP pages on
  -s, --service VALUE                Service control action: status, install, uninstall, start, stop, restart, reload (configuration)
  -l, --logfile VALUE                Path to log file. If empty: write to stdout; if 'syslog': write to system log
  --pidfile VALUE                    Path to a file where PID is stored
  --check-config                     Check configuration and exit
  --no-check-update                  Don't check for updates
  --no-mem-optimization              Disable memory optimization
  --no-etc-hosts                     Do not use the OS-provided hosts.
  -v, --verbose                      Enable verbose output
  --glinet                           Run in GL-Inet compatibility mode
  --version                          Show the version and exit. Show more detailed version description with -v
  --help                             Print this help
```

Please note, that the command-line arguments override settings from the configuration file.

`./AdGuardHome -s reload` command does the following:

- Re-read runtime clients from `/etc/hosts` file and `arp -a` output
- Re-read SSL certificate file (if it has changed)

Command-line arguments passed to `-s install` command will be then used by the service. For instance if you install AdGuardHome service using `sudo ./AdGuardHome -s install --no-check-update`, it will disable update check functionality in the service as well.

<a id="upstreams"></a>

## Configuring upstreams

AdGuard Home is basically a DNS proxy that sends your DNS queries to the upstream servers. You can specify multiple upstream servers in AdGuard Home settings, or you can even specify a DNS server that will be used to resolve specific domains.

Examples:

- `1.1.1.1` - regular DNS (over UDP).
- `tls://1dot1dot1dot1.cloudflare-dns.com` – encrypted [DNS-over-TLS](https://en.wikipedia.org/wiki/DNS_over_TLS).
- `https://cloudflare-dns.com/dns-query` – encrypted [DNS-over-HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS).
- `quic://dns-unfiltered.adguard.com:784` - experimental [DNS-over-QUIC](https://tools.ietf.org/html/draft-huitema-quic-dnsoquic) support.
- `tcp://1.1.1.1` – regular DNS (over TCP).
- `sdns://...` – you can use [DNS Stamps](https://dnscrypt.info/stamps/) for [DNSCrypt](https://dnscrypt.info/) or [DNS-over-HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS) resolvers.
- `[/example.local/]1.1.1.1` – you can specify DNS upstream for a specific domain(s).

<a id="upstreams-for-domains"></a>

### Specifying upstreams for domains

You can specify upstreams that will be used for a specific domain(s). We use the dnsmasq-like syntax (see `--server` description [here](http://www.thekelleys.org.uk/dnsmasq/docs/dnsmasq-man.html)). This feature is intended for private nameservers which deal with intranet domains.

**Syntax:**

    [/domain1/domain2/domainN/]upstreamString

If one or more domains are specified, that upstream (`upstreamString`) is used only for those domains. Usually, it is used for private nameservers. For instance, if you have a nameserver on your network which deals with `xxx.internal.local` at `192.168.0.1` then you can specify `[/internal.local/]192.168.0.1`, and AdGuard Home will send all queries to that nameserver. Everything else will be sent to the default upstreams (which are mandatory!).

1. An empty domain specification, // has the special meaning of "unqualified names only" ie names without any dots in them.
2. More specific domains take precedence over less specific domains, so: `[/host.com/]1.2.3.4` + `[/www.host.com/]2.3.4.5` will send queries for _.host.com to 1.2.3.4, except _.www.host.com, which will go to 2.3.4.5
3. The special server address '#' means, "use the standard servers", so: `[/host.com/]1.2.3.4` + `[/www.host.com/]#` will send queries for _.host.com to 1.2.3.4, except _.www.host.com which will be forwarded as usual.

#### Examples

- ```
  8.8.8.8:53
  [/local/]192.168.0.1:53
  ```

  Sends queries for `*.local` domains to `192.168.0.1:53`. Other queries are sent to `8.8.8.8:53`.

- ```
  8.8.8.8:53
  [/host.com/]1.1.1.1:53
  [/maps.host.com/]#
  ```
  Sends queries for `*.host.com` to `1.1.1.1:53` except for `*.maps.host.com` which are sent to `8.8.8.8:53` (as long as other queries).

<a id="upstreams-from-file"></a>

### Loading the list of upstream servers from a file

Using specific upstreams for some domains is a common way to accelerate internet in China, for example see https://github.com/felixonmars/dnsmasq-china-list and many other dnsmasq lists.

These lists can be easily converted to a list for AdGuard Home:

- `server=/0-100.com/114.114.114.114` -> `[/0-100.com/]114.114.114.114`

The problem with these lists is that they may be too large. In this case you may want to load them from a separate file instead of setting all upstreams in AdGuard settings. Just specify the path to a file with your list in the `upstream_dns_file` field of `AdGuardHome.yaml`.

<a id="upstreams-for-rdns"></a>

### Specifying upstreams for rDNS

AdGuardHome automatically gets the names of connected devices using Reverse DNS lookup (rDNS).
It sends a PTR request with an IP address of a client to a DNS server and uses its name for "clients friendly names".

Since **v0.106.0** you can enable and disable this feature by "Enable clients' hostname resolution" setting in the "Upstream DNS servers" section or via `resolve_clients` field in the configuration file. Also since **v0.106.0** all the addresses from [private IP range](https://tools.ietf.org/html/rfc6303) are only resolved via appropriate local resolvers to avoid the leaks of clients' information. But you can also set custom upstreams for it by "Private DNS servers" field in the "Upstream DNS servers" section or by `local_ptr_upstreams` field in the configuration file. Note that the specified upstreams are also used by DNS server to resolve PTR for the same IP range.

But what if you want AdGuardHome to use another DNS server for a specific IP address range?
You can do it using the same syntax as for general upstream servers, for example:

    [/128.in-addr.arpa/]8.8.8.8

This rule instructs AdGuardHome to use `8.8.8.8` DNS server for all rDNS requests to resolve clients' IP addresses `128.0.0.0/8`.

<a id="configuration-file"></a>

## Configuration file

Upon the first execution, a file named `AdGuardHome.yaml` will be created, with default values written in it. You can modify the file while your AdGuard Home service is not running. Otherwise, any changes to the file will be lost because the running program will overwrite them.

Settings are stored in [YAML format](https://en.wikipedia.org/wiki/YAML), possible parameters that you can configure are listed below:

- `bind_host` — Web interface IP address to listen on.
- `bind_port` — Web interface IP port to listen on.
- `users` — Web users info
  - `name` — User name
  - `password` — BCrypt-encrypted password
- `http_proxy` — proxy URL for HTTP client (e.g. "http://user:password@server:port/"). Supports "http", "https" and "socks5" scheme.
- `web_session_ttl` — Web session TTL (in hours) - a web user will stay signed in for this amount of time.
- `rlimit_nofile` — Limit on the maximum number of open files for server process (Linux and macOS). Set to 0 to use the system default value.
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
    - `statistics_interval` - time interval for statistics (in days)
  - **Protection settings**
    - `protection_enabled` — Whether any kind of filtering and protection should be done, when off it works as a plain dns forwarder.
    - `filtering_enabled` — Filtering of DNS requests based on filter lists.
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
    - `querylog_interval` - Time interval for query log (in days)
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
    - `local_ptr_upstreams` (**since v0.106.0**) – List of upstream DNS servers
      to resolve PTR requests for addresses inside locally-served networks. If
      empty, AdGuard Home will automatically try to get local resolvers from the
      OS.
    - `all_servers` — Enables parallel queries to all configured upstream servers to speed up resolving. If disabled, the queries are sent to each upstream server one-by-one and then sorted by RTT.
    - `fastest_addr` — Use Fastest Address algorithm. It finds an IP address with the lowest latency and returns this IP address in DNS response.
  - **ECS settings**
    - `edns_client_subnet` — Enable EDNS Client Subnet option. If enabled, AdGuard Home will be sending ECS extension to the upstream DNS servers. Please note, that this will be done for clients with public IP addresses only.
  - **Access settings**
    - `allowed_clients` — IP addresses of allowed clients
    - `disallowed_clients` — IP addresses of clients that should be blocked
    - `blocked_hosts` — hosts that should be blocked
  - **DNS cache settings**
    - `cache_size` — DNS cache size (in bytes)
    - `cache_ttl_min` — override TTL value (minimum) received from upstream server. This value can't larger than 3600 (1 hour).
    - `cache_ttl_max` — override TTL value (maximum) received from upstream server
  - **Other settings**
    - `bogus_nxdomain` - Transform responses with these IP addresses to NXDOMAIN
    - `enable_dnssec` - Set DNSSEC flag in the outgoing DNS requests and check the result
    - `aaaa_disabled` — Respond with an empty answer to all AAAA requests
    - `safebrowsing_cache_size` — Safe Browsing cache size (in bytes)
    - `safesearch_cache_size` — Safe Search cache size (in bytes)
    - `parental_cache_size` — Parental Control cache size (in bytes)
    - `cache_time` — Safe Browsing, Safe Search, Parental Control cache TTL
    - `max_goroutines` — Max. number of parallel goroutines for processing incoming requests
    - `ipset` — List of domain-ipset_name associations for adding IP addresses of the specified domain names to an ipset list. Syntax: `DOMAIN[,DOMAIN].../IPSET_NAME[,IPSET_NAME]...`. IPv4 addresses are added to an ipset list with `ipv4` family, IPv6 addresses - to `ipv6` ipset list. ipset list must exist. This setting is supported on Linux only. This feature is similar to `--ipset` in dnsmasq.
    - `autohost_tld` (**since v0.106.0**) — The top-level domain that AdGuard
      Home uses for known internal hosts.  The default value, which is also set
      when this value is empty, is `lan`.  So, if you have a machine called
      `myhost` in your network, its automatic hostname will be `myhost.lan`.
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
- `tls` - HTTPS/DOH/DOT settings.
  - `enabled` - encryption (DOT/DOH/HTTPS) status.
  - `server_name` - the hostname of your HTTPS/TLS server.
  - `force_https` - if true, forces HTTP->HTTPS redirect.
  - `port_https` - HTTPS port.  If `0`, HTTPS is disabled.
  - `port_dns_over_tls` - DNS-over-TLS port.  If `0`, DOT is disabled.
  - `port_dns_over_quic` - DNS-over-QUIC port.  If `0`, DOQ is disabled.
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

<a id="password-reset"></a>

## Reset Web Password

AdGuard Home stores password as a BCrypt-encoded hash.

Here's what you need to do to change the password:

1. Stop AdGuard Home
2. Edit `AdGuardHome.yaml`
3. Find `password` field there
4. Replace it with the new value. You can use `htpasswd` tool or any online BCrypt generation tool (there are many available online).
5. Start AdGuard Home

Now you'll be able to log in to Web interface using your new password.

Example using htpasswd:
```
htpasswd -bnBC 10 "" MY_NEW_PASS | tr -d ':'
```

<a id="pprof"></a>

## Profiling with pprof

To enable pprof, set `debug_pprof: true` in yaml configuration file and then restart AdGuardHome. Now you can get profiling information with your browser, e.g.:

    http://localhost:6060/debug/pprof/goroutine?debug=2

will show the call trace of each running goroutine.

This command lets you see information about the process's heap usage:

    http://localhost:6060/debug/pprof/heap?debug=1

or with `go tool pprof`:

    go tool pprof -top http://localhost:6060/debug/pprof/heap

For a list of supported profiles go to `http://localhost:6060/debug/pprof/`.

Alternatively, you may want to simply download the file and analyze it later:

```
wget http://localhost:6060/debug/pprof/heap
go tool -http=:8080 heap
```
