# AdGuard Home - Configuration

Most of these settings can be changed via the web-based admin interface. However, we decided to list them all here just in case.

## Command-line arguments

Here is a list of all available command-line arguments.

```
$ ./AdGuardHome --help
Usage:

./AdGuardHome [options]

Options:
  -c, --config                       path to the config file
  -w, --work-dir                     path to the working directory
  -h, --host                         host address to bind HTTP server on
  -p, --port                         port to serve HTTP pages on
  -s, --service                      service control action: status, install, uninstall, start, stop, restart
  -l, --logfile                      path to the log file. If empty, writes to stdout, if 'syslog' -- system log
  -v, --verbose                      enable verbose output
  --help                             print this help
```

Please note, that the command-line arguments override settings from the configuration file.

## Configuration file

Upon the first execution, a file named `AdGuardHome.yaml` will be created, with default values written in it. You can modify the file while your AdGuard Home service is not running. Otherwise, any changes to the file will be lost because the running program will overwrite them.

Settings are stored in [YAML format](https://en.wikipedia.org/wiki/YAML), possible parameters that you can configure are listed below:

 * `bind_host` — Web interface IP address to listen on.
 * `bind_port` — Web interface IP port to listen on.
 * `auth_name` — Web interface optional authorization username.
 * `auth_pass` — Web interface optional authorization password.
 * `dns` — DNS configuration section.
   * `bind_host` - DNS interface IP address to listen on. 
   * `port` — DNS server port to listen on.
   * `protection_enabled` — Whether any kind of filtering and protection should be done, when off it works as a plain dns forwarder.
   * `filtering_enabled` — Filtering of DNS requests based on filter lists.
   * `blocked_response_ttl` — For how many seconds the clients should cache a filtered response. Low values are useful on LAN if you change filters very often, high values are useful to increase performance and save traffic.
   * `querylog_enabled` — Query logging (also used to calculate top 50 clients, blocked domains and requested domains for statistical purposes).
   * `ratelimit` — DDoS protection, specifies in how many packets per second a client should receive. Anything above that is silently dropped. To disable set 0, default is 20. Safe to disable if DNS server is not available from internet.
   * `ratelimit_whitelist` — If you want exclude some IP addresses from ratelimiting but keep ratelimiting on for others, put them here.
   * `refuse_any` — Another DDoS protection mechanism. Requests of type ANY are rarely needed, so refusing to serve them mitigates against attackers trying to use your DNS as a reflection. Safe to disable if DNS server is not available from internet.
   * `bootstrap_dns` — List of DNS servers used for initial hostname resolution in case an upstream server name is a hostname.
   * `all_servers` — Enables parallel queries to all configured upstream servers to speed up resolving. If disabled, the queries are sent to each upstream server one-by-one and then sorted by RTT.
   * `parental_sensitivity` — Age group for parental control-based filtering, must be either 3, 10, 13 or 17 if enabled.
   * `parental_enabled` — Parental control-based DNS requests filtering.
   * `safesearch_enabled` — Enforcing "Safe search" option for search engines, when possible.
   * `safebrowsing_enabled` — Filtering of DNS requests based on safebrowsing.
   * `upstream_dns` — List of upstream DNS servers.
 * `filters` — List of filters, each filter has the following values:
   * `enabled` — Current filter's status (enabled/disabled).
   * `url` — URL pointing to the filter contents (filtering rules).
   * `name` — Name of the filter. If it's an adguard syntax filter it will get updated automatically, otherwise it stays unchanged.
   * `last_updated` — Time when the filter was last updated from server.
   * `ID` - filter ID (must be unique).
 * `dhcp` - Built-in DHCP server configuration.
   * `enabled` - DHCP server status.
   * `interface_name` - network interface name (eth0, en0 and so on).
   * `gateway_ip` - gateway IP address.
   * `subnet_mask` - subnet mask.
   * `range_start` - start IP address of the controlled range.  
   * `range_end` - end IP address of the controlled range.
   * `lease_duration` - lease duration in seconds. If 0, using default duration (2 hours).
 * `tls` - HTTPS/DOH/DOT settings.
   * `enabled` - encryption (DOT/DOH/HTTPS) status.
   * `server_name` - the hostname of your HTTPS/TLS server.
   * `force_https` - if true, forces HTTP->HTTPS redirect.
   * `port_https` - HTTPS port. If 0, HTTPS will be disabled.
   * `port_dns_over_tls` - DNS-over-TLS port. If 0, DOT will be disabled.
   * `certificate_chain` - PEM-encoded certificates chain.
   * `private_key` - PEM-encoded private key.
 * `user_rules` — User-specified filtering rules.
 * `log_file` — Path to the log file. If empty, writes to stdout, if `syslog` -- system log (or eventlog on Windows).
 * `verbose` — Enable our disables debug verbose output.

Removing an entry from settings file will reset it to the default value. Deleting the file will reset all settings to the default values.

## Configuring clients friendly names

It may be useful to set up a friendly name for each IP address that clients use to connect to AdGuard Home.  To do this, follow these steps:

1. Open `/etc/hosts` file in your text editor and add a name for each IP address, for example:

		192.168.0.2    Mom
		192.168.0.3    Dad
		192.168.0.4    Sister
		192.168.0.5    Brother

	Note that on Windows file path to "hosts" file is different: `c:\windows\system32\drivers\etc\hosts`

2. Restart AdGuard Home

As a result you will see that the clients names are now shown:

![](top-clients-names.png)

In the future, we plan to add more configuration options in this area.
