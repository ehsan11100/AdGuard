 # AdGuard Home - How to Write Hosts Blocklists

* [Introduction](#introduction)
* [Rules examples](#examples)
* [Adblock-style syntax](#adblock-style)
    * [Special characters](#special-characters)
    * [Regular expressions support](#regular-expressions)
    * [Rule modifiers](#modifiers)
        * [client](#client)
        * [dnstype](#dnstype)
        * [dnsrewrite](#dnsrewrite)
        * [important](#important)
        * [badfilter](#badfilter)
        * [ctag](#ctag)
* [/etc/hosts syntax](#etc-hosts)
* [Domains-only syntax](#domains-only)
* [Hostlists compiler](#hostlists-compiler)

## <a id="introduction"></a> Introduction

There are three different approaches to writing hosts blocklists:

- [Adblock-style syntax](#adblock-style) - modern approach to writing filtering rules based on using a subset of the Adblock-style syntax. This way blocklists will be compatible with browser ad blockers.
- [/etc/hosts syntax](#etc-hosts) - the old, tried and true approach is to use the same syntax as Operation Systems use for the "hosts" files.
- [domains-only syntax](#domains-only) - a list of domain names.

If you are creating a blocklist for AdGuard Home, we recommend using the [Adblock-style syntax](#adblock-style). It has a couple of important advantages over the old-style syntax:

- **Blocklists size.** Using pattern-matching allows you to have a single rule instead of hundreds of `/etc/hosts` entries.
- **Compatibility.** Your blocklist will be compatible with browser ad blockers, and it will be easier to share rules with a browser filter list.
- **Extensibility.** For the last decade the Adblock-style syntax has greatly evolved, and we don't see why we can't extend it even more, and provide additional features for network-wide blockers.

> If you're maintaining an old-style `/etc/hosts` blocklist or if you maintain multiple filter lists (regardless of which type), we provide a tool that can be used to compile blocklists for AdGuard Home. We called it [Hostlist compiler](https://github.com/AdguardTeam/HostlistCompiler) and we use it ourselves to create [AdGuard SDN filter](https://github.com/AdguardTeam/AdGuardSDNSFilter).

## <a id="rules-examples"></a> Rules examples

- `||example.org^` - block access to the `example.org` domain and all its subdomains
- `@@||example.org^` - unblock access to the `example.org` domain and all its subdomains
- `0.0.0.0 example.org` - (attention, old-style /etc/hosts syntax) block `example.org` domain (but NOT its subdomains)
- `! Here goes a comment` - just a comment
- `# Also a comment` - just a comment
- `/REGEX/` - block access to the domains matching the specified regular expression

## <a id="adblock-style"></a> Adblock-style syntax

This is a subset of the [traditional Adblock-style](https://kb.adguard.com/en/general/how-to-create-your-own-ad-filters) syntax which is used by browser ad blockers.

```
      rule = ["@@"] pattern [ "$" modifiers ]
modifiers = [modifier0, modifier1[, ...[, modifierN]]]
```

- `pattern` — the hostname mask. Every hostname is matched against this mask. The pattern can also contain special characters, which are described below.
- `@@` — a marker that is used in the "exception" rules. Start your rule with this marker if you want to turn off filtering for the matching hostnames.
- `modifiers` — parameters that clarify the rule. They may limit the scope of the rule or even completely change the way it works.

### <a id="special-characters"></a> Special characters

- `*` — wildcard character. It is used to represent "any set of characters". This can also be an empty string or a string of any length.
- `||` — matching the beginning of a hostname (and any subdomain). For instance, `||example.org` matches `example.org` and `test.example.org`, but not `testexample.org`.
- `^` — separator character mark. Unlike browser ad blocking, there's nothing to "separate" in a hostname, so the only purpose of this character is to mark the end of the hostname.
- `|` — a pointer to the beginning or the end of the hostname. The value depends on the character placement in the mask. For example, the rule `ample.org|` corresponds to `example.org`, but not to `example.org.com`. `|example` corresponds to `example.org`, but not to `test.example`.

### <a id="regular-expressions"></a> Regular expressions support

If you want even more flexibility in making rules, you can use [Regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) instead of the default simplified matching syntax.

If you want to use a regular expression, the pattern has to look like this:

```
pattern = "/" regexp "/"
```

### <a id="comments"></a> Comments

Any line that starts with an exclamation mark is a comment and it will be ignored by the filtering engine. Comments are usually placed above rules and used to describe what a rule does.

```
! This is a comment
```

**Examples:**

- `/example.*/` will block hosts matching the `example.*` regex.
- `@@/example.*/$important` will unblock hosts matching the `example.*` regex. Note that this rule also has the `$important` modifier.

### <a id="modifiers"></a> Rule modifiers

You can change the behavior of a rule by using additional modifiers. Modifiers must be located at the end of the rule after the `$` character and be separated by commas.

Example:

```
||example.org^$important
```

- `||example.org^` - a matching pattern
- `$` - a delimiter, it signals that now modifiers start
- `important` - a modifier

> **IMPORTANT:** If a rule contains a modifier not listed in this document, the whole rule **must be ignored**. This way we will avoid false-positives when people are trying to use unmodified browser ad blockers' filter lists like EasyList or EasyPrivacy.

#### <a id="client"></a> `client`

The `$client` modifier allows specifying clients this rule will be working for. It accepts both client names, IP addresses or CIDR ranges.

The syntax is:

```
$client=value1|value2|...
```

You can also specify "excluded" clients by adding a `~` character before the client IP or name. In this case, the rule will not be applied to this client's DNS requests.

```
$client=~value1
```

**Specifying client names**

Client names usually contain spaces or other special characters, that's why you should enclose the name in quotes (both double-quotes and single-quotes are supported). If the client name contains quotes, use `\` to escape them. Also, you need to escape commas (`,`) and pipes (`|`).

> Please note, that when specifying an "excluded" client, you must keep `~` out of the quotes.

**Examples**

* `@@||*^$client=127.0.0.1` — unblock everything for localhost
* `||example.org^$client='Frank\'s laptop'` — block `example.org` for the client named `Frank's laptop` only. Note that quote (`'`) in the name must be escaped.
* `||example.org^$client=~'Mary\'s\, John\'s\, and Boris\'s laptops'` — block `example.org` for everyone except the client named `Mary's, John's, and Boris's laptops`. Note that comma (`,`) must be escaped as well.
* `||example.org^$client=~Mom|~Dad|Kids` -- block `example.org` for `Kids`, but not for `Mom` and `Dad`. This example demonstrates how to specify multiple clients in one rule.
* `||example.org^$client=192.168.0.0/24` -- block `example.org` for all clients with IP addresses in the range `192.168.0.0-192.168.0.255`

#### <a id="dnstype"></a> `dnstype`

(Since **v0.105.0**.)

The `$dnstype` modifier allows specifying DNS request type on which this rule
will be triggered.

The syntax is:

```
$dnstype=value1|value2|...
$dnstype=~value1|~value2|~...
```

The names of the types are case-insensitive, but are validated against a set of
actual DNS resource record (RR) types.

This:

```
$dnstype=~value1|value2
```

Is equivalent to this:

```
$dnstype=value2
```

**Examples**

* `||example.org^$dnstype=AAAA` — Block IPv6 DNS requests for `example.org`.
* `||example.org^$dnstype=~A|~CNAME` — Only allow `A` and `CNAME` DNS requests
  for `example.org`, block out the rest.

#### <a id="dnsrewrite"></a> `dnsrewrite`

(Since **v0.105.0**.)

The `$dnsrewrite` response modifier allows replacing the content of the response
to the DNS request for the matching hosts.

The shorthand syntax is:

```
$dnsrewrite=1.2.3.4
$dnsrewrite=abcd::1234
$dnsrewrite=example.net
$dnsrewrite=REFUSED
```

The keywords, like `REFUSED`, MUST be in all caps.

The full syntax is of the form `RCODE;RRTYPE;VALUE`:

```
$dnsrewrite=NOERROR;A;1.2.3.4
$dnsrewrite=NOERROR;AAAA;abcd::1234
$dnsrewrite=NOERROR;CNAME;example.net
$dnsrewrite=REFUSED;;
```

The `CNAME` one is special because _AdGuardHome_ will resolve the host and add
its info to the response.  That is, if example.net has IP `1.2.3.4`, and the
user has this in their filter rules:

```
||example.com^$dnsrewrite=example.net
```

Or:

```
||example.com^$dnsrewrite=NOERROR;CNAME;example.net
```

Then the response will be something like:

```
$ nslookup example.com my.adguard.local
Server:		my.adguard.local
Address:	127.0.0.1#53

Non-authoritative answer:
example.com	canonical name = example.net.
Name:	example.net
Address: 1.2.3.4
```

Keyword rewrites (for example, `REFUSED`) take precedence over the other.  Next,
the `CNAME` rewrite.  After that, all other records's values are summed as one
response, so this:

```
||example.com^$dnsrewrite=NOERROR;A;1.2.3.4
||example.com^$dnsrewrite=NOERROR;A;1.2.3.5
```

Will result in a response with two `A` records.

Currently supported RR types with examples:

* `||4.3.2.1.in-addr.arpa.^$dnsrewrite=NOERROR;PTR;example.net.` adds a `PTR`
  record for reverse DNS.  Reverse DNS requests for `1.2.3.4` to the
  _AdGuardHome_ DNS server will result in `example.net`.

  **NOTE:** the IP MUST be in reverse order, and the value MUST contain a final
  dot.  See [RFC](https://tools.ietf.org/html/rfc1035#section-3.5).

* `||example.com^$dnsrewrite=NOERROR;A;1.2.3.4` adds an `A` record with the
  value `1.2.3.4`.

* `||example.com^$dnsrewrite=NOERROR;AAAA;abcd::1234` adds an `AAAA` record with
  the value `abcd::1234`.

* `||example.com^$dnsrewrite=NOERROR;CNAME;example.org` adds a `CNAME` record.
  See explanation above.

* `||example.com^$dnsrewrite=NOERROR;HTTPS;32 example.com alpn=h3` adds an
  `HTTPS` record.  Only a subset of parameter values is supported: values must
  be `contiguous` and, where a `value-list` is expected`, only one value is
  currently supported:

  ```
  ipv4hint=127.0.0.1             // Supported.
  ipv4hint="127.0.0.1"           // Unsupported.
  ipv4hint=127.0.0.1,127.0.0.2   // Unsupported.
  ipv4hint="127.0.0.1,127.0.0.2" // Unsupported.
  ```

  This will change in the future.

* `||example.com^$dnsrewrite=NOERROR;MX;32 example.mail` adds an `MX` record
  with precedence value `32` and exchange value `example.mail`.

* `||example.com^$dnsrewrite=NOERROR;SVCB;32 example.com alpn=h3` adds and
  `SVCB` value.  See `HTTPS` above.

* `||example.com^$dnsrewrite=NOERROR;TXT;hello_world` adds a `TXT` record with
  the value `hello_world`.

* `||example.com^$dnsrewrite=NXDOMAIN;;` responds with an `NXDOMAIN` code.

Exception rules remove one or all rules:

* `||example.com^$dnsrewrite` removes all DNS rewrite rules.

* `||example.com^$dnsrewrite=1.2.3.4` removes the DNS rewrite rule that adds an
  `A` record with the value `1.2.3.4`.

#### <a id="important"></a> <a id="important"></a> `important`

The `$important` modifier applied to a rule increases its priority over any other rule without \$important modifier. Even over basic exception rules.

**Example 1:**

```
||example.org^$important
@@||example.org^
```

`||example.org^$important` will block all requests despite the exception rule.

**Example 2:**

```
||example.org^$important
@@||example.org^$important
```

Now the exception rule also has the `$important` modifier so it will prevail.

#### <a id="badfilter"></a> `badfilter`

The rules with the `$badfilter` modifier disable other basic rules to which they refer. It means that the text of the disabled rule should match the text of the `$badfilter` rule (without the `badfilter` modifier).

**Examples:**

- `||example.com$badfilter` disables `||example.com`
- `@@||example.org^$badfilter` disables `@@||example.org^`

#### <a id="ctag"></a> `ctag`

`$ctag` modifier allows to block domains only for specific types of DNS clients. You can assign tags to clients in AdGuardHome UI. In the future we plan to assign tags automatically by analyzing the behavior of each client.

The syntax is:

    $ctag=value1|value2|...

If one of client's tags matches the `$ctag` values - this rule applies to this client.

The syntax for exclusion is:

    $ctag=~value1|~value2|...

If one of client's tags matches the exclusion `$ctag` values - this rule doesn't apply to this client.

**Examples:**

- `||example.org^$ctag=device_pc|device_phone` - block `example.org` for clients tagged as `device_pc` or `device_phone`
- `||example.org^$ctag=~device_phone` - block `example.org` for all clients except those tagged as `device_phone`

The list of allowed tags:

By device type:

- `device_audio` - Audio device
- `device_camera` - Camera device
- `device_gameconsole` - Game Console
- `device_laptop` - Laptop
- `device_nas` - NAS (Network-attached Storage)
- `device_other` - Other device
- `device_pc` - PC
- `device_phone` - Phone
- `device_printer` - Printer
- `device_securityalarm` - Security alarm
- `device_tablet` - Tablet
- `device_tv` - TV

By Operating System:

- `os_android` - Android
- `os_ios` - iOS
- `os_linux` - Linux
- `os_macos` - Macos
- `os_other` - Other OS
- `os_windows` - Windows

By user group:

- `user_admin` - Administrator
- `user_child` - Child
- `user_regular` - Regular user

## <a id="etc-hosts"></a> /etc/hosts syntax

For each host a single line should be present with the following information:

```
IP_address canonical_hostname [aliases...]
```

Fields of the entry are separated by any number of blanks and/or tab characters.

Text from the `#` character until the end of the line is a comment and is ignored.

Example:

```
# This is a comment
```

Hostnames may contain only alphanumeric characters, minus signs (`-`), and periods (`.`). They must begin with an alphabetic character and end with an alphanumeric character. Optional aliases provide for name changes, alternate spellings, shorter hostnames, or generic hostnames (for example, `localhost`).

Examples:

```
127.0.0.1 example.org foo
127.0.0.1 example.com
```

> Please note, that the `IP_address` value is ignored by most of the DNS filtering software.

## <a id="domains-only"></a> Domains-only syntax

This is just a list of domain names, one name per line.

Example:

```
example.com
example.org
```

If a string is not a valid domain (e.g. `*.example.org`), AdGuard Home will consider it to be an [adblock-style](#adblock-style) rule.

## <a id="hostlists-compiler"></a> Hostlists compiler

If you are maintaining a blocklist and use different sources in it, [Hostlists compiler](https://github.com/AdguardTeam/HostlistCompiler) may be useful to you.

This is a simple tool that makes it easier to compile a hosts blocklist compatible with AdGuard Home or any other AdGuard product with DNS filtering.

What it's capable of:

1. Compile a single blocklist from multiple sources.
2. Exclude the rules you don't need.
3. Cleanup the resulting list: deduplicate, remove invalid rules, compress the list.
