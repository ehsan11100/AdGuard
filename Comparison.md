<a id="comparison"></a>
## Comparing AdGuard Home to Other Solutions

<a id="comparison-adguard-dns"></a>
### How is this different from public AdGuard DNS servers?

Running your own AdGuard Home server allows you to do much more than using a public DNS server. It's a completely different level. See for yourself:

* Choose what exactly will the server block or not block.
* Monitor your network activity.
* Add your own custom filtering rules.
* **Most importantly, this is your own server, and you are the only one who's in control.**

<a id="comparison-pi-hole"></a>
### How does AdGuard Home compare to Pi-Hole

At this point, AdGuard Home has a lot in common with Pi-Hole. Both block ads and trackers using "DNS sinkholing" method, and both allow customizing what's blocked.

> We're not going to stop here. DNS sinkholing is not a bad starting point, but this is just the beginning.

AdGuard Home provides a lot of features out-of-the-box with no need to install and configure additional software. We want it to be simple to the point when even casual users can set it up with minimal effort.

> Disclaimer: some of the listed features can be added to Pi-Hole by installing additional software or by manually using SSH terminal and reconfiguring one of the utilities Pi-Hole consists of. However, in our opinion, this cannot be legitimately counted as a Pi-Hole's feature.

| Feature                                                                 | AdGuard&nbsp;Home | Pi-Hole                                                |
|-------------------------------------------------------------------------|--------------|--------------------------------------------------------|
| Blocking ads and trackers                                               | ✅            | ✅                                                      |
| Customizing blocklists                                                  | ✅            | ✅                                                      |
| Built-in DHCP server                                                    | ✅            | ✅                                                      |
| HTTPS for the Admin interface                                           | ✅            | Kind of, but you'll need to manually configure lighthttpd |
| Encrypted DNS upstream servers (DNS-over-HTTPS, DNS-over-TLS, DNSCrypt) | ✅            | ❌ (requires additional software)                       |
| Cross-platform                                                          | ✅            | ❌ (not natively, only via Docker)                      |
| Running as a DNS-over-HTTPS or DNS-over-TLS server                      | ✅            | ❌ (requires additional software)                       |
| Blocking phishing and malware domains                                   | ✅            | ❌                                                      |
| Parental control (blocking adult domains)                               | ✅            | ❌                                                      |
| Force Safe search on search engines                                     | ✅            | ❌                                                      |
| Per-client (device) configuration                                       | ✅            | ❌                                                      |
| Access settings (choose who can use AGH DNS)                            | ✅            | ❌                                                      |

<a id="comparison-adblock"></a>
### How does AdGuard Home compare to traditional ad blockers

It depends.

"DNS sinkholing" is capable of blocking a big percentage of ads, but it lacks flexibility and power of traditional ad blockers. You can get a good impression about the difference between these methods by reading [this article](https://adguard.com/en/blog/adguard-vs-adaway-dns66/). It compares AdGuard for Android (a traditional ad blocker) to hosts-level ad blockers (which are almost identical to DNS-based blockers in their capabilities). However, this level of protection is enough for some users.
