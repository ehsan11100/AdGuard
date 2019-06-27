# Recursive DNS server

A recursive DNS server doesn't require an upstream server to resolve a domain name (e.g. `www.hostname.com`).  Instead, it starts with Root DNS servers and then recursively asks authoritative DNS servers for each part of the domain name (e.g. `com`, then `hostname.com`).  Finally, it reaches the specific authoritative server which is responsible for the target domain name (e.g. `www.hostname.com`).  One such recursive DNS server is `unbound`.  You can read more information about it here: https://nlnetlabs.nl/projects/unbound/about/.

AdGuard Home can work with `unbound` on several system configurations (currently, Linux-amd64 and Linux-arm).


## Enable recursive DNS server

To enable recursive DNS server, add `unbound` to the "Upstream DNS servers" list in UI.
If you want to use it for specific domains only, use `[/hostname/]unbound` setting.


## Configure libunbound

libunbound has many configuration settings that you can set in `AdGuardHome.yaml` file.  The list of supported settings is located here: https://nlnetlabs.nl/documentation/unbound/unbound.conf/

For example, to set libunbound cache size equal to 8 megabytes, use this setting:

	dns:
	...
	  unbound_settings:
	  - 'msg-cache-size: 8m'
