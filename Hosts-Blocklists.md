# AdGuard Home - How to write hosts blocklists

There are three different approaches to writing hosts blocklists:

- [/etc/hosts syntax](#etc-hosts) - the old, tried and true approach is to use the same syntax as Operation Systems use for the "hosts" files.
- [domains-only syntax](#domains-only) - a list of domain names.
- [Adblock-style syntax](#adblock-style) - modern approach to writing filtering rules based on using a subset of the Adblock-style syntax. This way blocklists will be compatible with browser ad blockers.

If you are creating a blocklist for AdGuard Home, we recommend using the [Adblock-style syntax](#adblock-style). It has a couple of important advantages over the old-style syntax:

- **Blocklists size.** Using pattern-matching allows you to have a single rule instead of hundreds of `/etc/hosts` entries.
- **Compatibility.** Your blocklist will be compatible with browser ad blockers, and it will be easier to share rules with a browser filter list.
- **Extensibility.** For the last decade the Adblock-style syntax has greatly evolved, and we don't see why we can't extend it even more, and provide additional features for network-wide blockers.

## Rules examples

- `||example.org^` - block access to the `example.org` domain and all its subdomains
- `@@||example.org^` - unblock access to the `example.org` domain and all its subdomains
- `0.0.0.0 example.org` - (attention, old-style /etc/hosts syntax) block `example.org` domain (but NOT its subdomains)
- `! Here goes a comment` - just a comment
- `# Also a comment` - just a comment
- `/REGEX/` - block access to the domains matching the specified regular expression

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


## <a id="adblock-style"></a> Adblock-style syntax

This is a subset of the [traditional Adblock-style](https://kb.adguard.com/en/general/how-to-create-your-own-ad-filters) syntax which is used by browser ad blockers.

```
      rule = ["@@"] pattern [ "$" modifiers ]
modifiers = [modifier0, modifier1[, ...[, modifierN]]]
```

- `pattern` — the hostname mask. Every hostname is matched against this mask. The pattern can also contain special characters, which are described below.
- `@@` — a marker that is used in the "exception" rules. Start your rule with this marker if you want to turn off filtering for the matching hostnames.
- `modifiers` — parameters that clarify the rule. They may limit the scope of the rule or even completely change the way it works.

### Special characters

- `*` — wildcard character. It is used to represent "any set of characters". This can also be an empty string or a string of any length.
- `||` — matching the beginning of a hostname (and any subdomain). For instance, `||example.org` matches `example.org` and `test.example.org`, but not `testexample.org`.
- `^` — separator character mark. Unlike browser ad blocking, there's nothing to "separate" in a hostname, so the only purpose of this character is to mark the end of the hostname.
- `|` — a pointer to the beginning or the end of the hostname. The value depends on the character placement in the mask. For example, the rule `ample.org|` corresponds to `example.org`, but not to `example.org.com`. `|example` corresponds to `example.org`, but not to `test.example`.

### Regular expressions support

If you want even more flexibility in making rules, you can use [Regular expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) instead of the default simplified matching syntax.

If you want to use a regular expression, the pattern has to look like this:

```
pattern = "/" regexp "/"
```

### Comments

Any line that starts with an exclamation mark is a comment and it will be ignored by the filtering engine. Comments are usually placed above rules and used to describe what a rule does.

```
! This is a comment
```

**Examples:**

- `/example.*/` will block hosts matching the `example.*` regex.
- `@@/example.*/$important` will unblock hosts matching the `example.*` regex. Note that this rule also has the `$important` modifier.

### Rule modifiers

You can change the behavior of a rule by using additional modifiers. Modifiers must be located at the end of the rule after the `$` character and be separated by commas.

Example:

```
||example.org^$important
```

- `||example.org^` - a matching pattern
- `$` - a delimiter, it signals that now modifiers start
- `important` - a modifier

> **IMPORTANT:** If a rule contains a modifier not listed in this document, the whole rule **must be ignored**. This way we will avoid false-positives when people are trying to use unmodified browser ad blockers' filter lists like EasyList or EasyPrivacy.

#### `important`

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

#### `badfilter`

The rules with the `$badfilter` modifier disable other basic rules to which they refer. It means that the text of the disabled rule should match the text of the `$badfilter` rule (without the `badfilter` modifier).

**Examples:**

- `||example.com$badfilter` disables `||example.com`
- `@@||example.org^$badfilter` disables `@@||example.org^`


#### <a id="ctag"></a> `ctag`

`$ctag` modifier allows to block domains only for specific types of DNS clients.  You can assign tags to clients in AdGuardHome UI.  In the future we plan to assign tags automatically by analyzing the behaviour of each client.

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
* device_audio - Audio device
* device_gameconsole - Game Console
* device_laptop - Laptop
* device_nas - NAS (Network-attached Storage)
* device_other - Other device
* device_pc - PC
* device_phone - Phone
* device_printer - Printer
* device_tablet - Tablet
* device_tv - TV

By Operating System:
* os_android - Android
* os_ios - iOS
* os_linux - Linux
* os_macos - Macos
* os_other - Other OS
* os_windows - Windows

By user group:
* user_admin - Administrator
* user_child - Child
* user_regular - Regular user
