 #  AdGuard Home - Supported Platforms

 *  [Changes](#changes)
 *  [Packaged releases](#releases)
 *  [Docker](#docker)
 *  [Snapcraft](#snap)



##  <a href="#changes" id="changes" name="changes">Changes</a>

 *  Support for Windows on 64-bit ARM was added in **v0.107.9.**

 *  Experimental support for OpenBSD on AMD64 and 64-bit ARM was added in
    **v0.107.0.**

 *  Support for Darwin / macOS and FreeBSD on 64-bit ARM was added in
    **v0.107.0.**



##  <a href="#releases" id="releases" name="releases">Packaged releases</a>

The official AdGuard Home packaged releases are listed in the table below:

<!--
For the link IDs in the table below, use the following abbreviations:

1.  The first letter stands for the release channel:

    *  `r` for release;
    *  `b` for beta;
    *  `e` for edge.

2.  The second letter stands for the CPU architecture:

    *  `a` for 64-bit AMD;
    *  `x` for x86;
    *  `r` for 64-bit ARM;
    *  `5` for ARMv5;
    *  `6` for ARMv6;
    *  `7` for ARMv7;
    *  `m` for 64-bit MIPS;
    *  `l` for 64-bit MIPS LE;
    *  `i` for 32-bit MIPS;
    *  `e` for 32-bit MIPS LE;
    *  `p` for 64-bit PowerPC.

3.  The third letter stands for the OS:

    *  `d` for Darwin / macOS;
    *  `f` for FreeBSD;
    *  `l` for Linux;
    *  `o` for OpenBSD;
    *  `w` for Windows.

TODO(a.garipov): Consider generating the table automatically.
-->

| CPU Architecture      | Darwin / macOS                                 | FreeBSD                                        | Linux                                          | OpenBSD                                        | Windows                                        |
| --------------------: | :--------------------------------------------: | :--------------------------------------------: | :--------------------------------------------: | :--------------------------------------------: | :--------------------------------------------: |
|             **AMD64** | [Release][rad]<br/>[Beta][bad]<br/>[Edge][ead] | [Release][raf]<br/>[Beta][baf]<br/>[Edge][eaf] | [Release][ral]<br/>[Beta][bal]<br/>[Edge][eal] | [Release][rao]<br/>[Beta][bao]<br/>[Edge][eao] | [Release][raw]<br/>[Beta][baw]<br/>[Edge][eaw] |
|               **x86** |                                                | [Release][rxf]<br/>[Beta][bxf]<br/>[Edge][exf] | [Release][rxl]<br/>[Beta][bxl]<br/>[Edge][exl] |                                                | [Release][rxw]<br/>[Beta][bxw]<br/>[Edge][exw] |
|        **64-bit ARM** | [Release][rrd]<br/>[Beta][brd]<br/>[Edge][erd] | [Release][rrf]<br/>[Beta][brf]<br/>[Edge][erf] | [Release][rrl]<br/>[Beta][brl]<br/>[Edge][erl] | [Release][rro]<br/>[Beta][bro]<br/>[Edge][ero] | [Release][rrw]<br/>[Beta][brw]<br/>[Edge][erw] |
|             **ARMv5** |                                                | [Release][r5f]<br/>[Beta][b5f]<br/>[Edge][e5f] | [Release][r5l]<br/>[Beta][b5l]<br/>[Edge][e5l] |                                                |                                                |
|             **ARMv6** |                                                | [Release][r6f]<br/>[Beta][b6f]<br/>[Edge][e6f] | [Release][r6l]<br/>[Beta][b6l]<br/>[Edge][e6l] |                                                |                                                |
|             **ARMv7** |                                                | [Release][r7f]<br/>[Beta][b7f]<br/>[Edge][e7f] | [Release][r7l]<br/>[Beta][b7l]<br/>[Edge][e7l] |                                                |                                                |
|       **64-bit MIPS** |                                                |                                                | [Release][rml]<br/>[Beta][bml]<br/>[Edge][eml] |                                                |                                                |
|    **64-bit MIPS LE** |                                                |                                                | [Release][rll]<br/>[Beta][bll]<br/>[Edge][ell] |                                                |                                                |
|       **32-bit MIPS** |                                                |                                                | [Release][ril]<br/>[Beta][bil]<br/>[Edge][eil] |                                                |                                                |
|    **32-bit MIPS LE** |                                                |                                                | [Release][rel]<br/>[Beta][bel]<br/>[Edge][eel] |                                                |                                                |
| **64-bit PowerPC LE** |                                                |                                                | [Release][rpl]<br/>[Beta][bpl]<br/>[Edge][epl] |                                                |                                                |

[r5f]: https://static.adguard.com/adguardhome/release/AdGuardHome_freebsd_armv5.tar.gz
[r5l]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_armv5.tar.gz
[r6f]: https://static.adguard.com/adguardhome/release/AdGuardHome_freebsd_armv6.tar.gz
[r6l]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_armv6.tar.gz
[r7f]: https://static.adguard.com/adguardhome/release/AdGuardHome_freebsd_armv7.tar.gz
[r7l]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_armv7.tar.gz
[rad]: https://static.adguard.com/adguardhome/release/AdGuardHome_darwin_amd64.zip
[raf]: https://static.adguard.com/adguardhome/release/AdGuardHome_freebsd_amd64.tar.gz
[ral]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_amd64.tar.gz
[rao]: https://static.adguard.com/adguardhome/release/AdGuardHome_openbsd_amd64.tar.gz
[raw]: https://static.adguard.com/adguardhome/release/AdGuardHome_windows_amd64.zip
[rel]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_mipsle_softfloat.tar.gz
[ril]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_mips_softfloat.tar.gz
[rll]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_mips64le_softfloat.tar.gz
[rml]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_mips64_softfloat.tar.gz
[rpl]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_ppc64le.tar.gz
[rrd]: https://static.adguard.com/adguardhome/release/AdGuardHome_darwin_arm64.zip
[rrf]: https://static.adguard.com/adguardhome/release/AdGuardHome_freebsd_arm64.tar.gz
[rrl]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_arm64.tar.gz
[rro]: https://static.adguard.com/adguardhome/release/AdGuardHome_openbsd_arm64.tar.gz
[rrw]: https://static.adguard.com/adguardhome/release/AdGuardHome_windows_arm64.zip
[rxf]: https://static.adguard.com/adguardhome/release/AdGuardHome_freebsd_386.tar.gz
[rxl]: https://static.adguard.com/adguardhome/release/AdGuardHome_linux_386.tar.gz
[rxw]: https://static.adguard.com/adguardhome/release/AdGuardHome_windows_386.zip

[b5f]: https://static.adguard.com/adguardhome/beta/AdGuardHome_freebsd_armv5.tar.gz
[b5l]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_armv5.tar.gz
[b6f]: https://static.adguard.com/adguardhome/beta/AdGuardHome_freebsd_armv6.tar.gz
[b6l]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_armv6.tar.gz
[b7f]: https://static.adguard.com/adguardhome/beta/AdGuardHome_freebsd_armv7.tar.gz
[b7l]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_armv7.tar.gz
[bad]: https://static.adguard.com/adguardhome/beta/AdGuardHome_darwin_amd64.zip
[baf]: https://static.adguard.com/adguardhome/beta/AdGuardHome_freebsd_amd64.tar.gz
[bal]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_amd64.tar.gz
[bao]: https://static.adguard.com/adguardhome/beta/AdGuardHome_openbsd_amd64.tar.gz
[baw]: https://static.adguard.com/adguardhome/beta/AdGuardHome_windows_amd64.zip
[bel]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_mipsle_softfloat.tar.gz
[bil]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_mips_softfloat.tar.gz
[bll]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_mips64le_softfloat.tar.gz
[bml]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_mips64_softfloat.tar.gz
[bpl]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_ppc64le.tar.gz
[brd]: https://static.adguard.com/adguardhome/beta/AdGuardHome_darwin_arm64.zip
[brf]: https://static.adguard.com/adguardhome/beta/AdGuardHome_freebsd_arm64.tar.gz
[brl]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_arm64.tar.gz
[bro]: https://static.adguard.com/adguardhome/beta/AdGuardHome_openbsd_arm64.tar.gz
[brw]: https://static.adguard.com/adguardhome/beta/AdGuardHome_windows_arm64.zip
[bxf]: https://static.adguard.com/adguardhome/beta/AdGuardHome_freebsd_386.tar.gz
[bxl]: https://static.adguard.com/adguardhome/beta/AdGuardHome_linux_386.tar.gz
[bxw]: https://static.adguard.com/adguardhome/beta/AdGuardHome_windows_386.zip

[e5f]: https://static.adguard.com/adguardhome/edge/AdGuardHome_freebsd_armv5.tar.gz
[e5l]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_armv5.tar.gz
[e6f]: https://static.adguard.com/adguardhome/edge/AdGuardHome_freebsd_armv6.tar.gz
[e6l]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_armv6.tar.gz
[e7f]: https://static.adguard.com/adguardhome/edge/AdGuardHome_freebsd_armv7.tar.gz
[e7l]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_armv7.tar.gz
[ead]: https://static.adguard.com/adguardhome/edge/AdGuardHome_darwin_amd64.zip
[eaf]: https://static.adguard.com/adguardhome/edge/AdGuardHome_freebsd_amd64.tar.gz
[eal]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_amd64.tar.gz
[eao]: https://static.adguard.com/adguardhome/edge/AdGuardHome_openbsd_amd64.tar.gz
[eaw]: https://static.adguard.com/adguardhome/edge/AdGuardHome_windows_amd64.zip
[eel]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_mipsle_softfloat.tar.gz
[eil]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_mips_softfloat.tar.gz
[ell]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_mips64le_softfloat.tar.gz
[eml]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_mips64_softfloat.tar.gz
[epl]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_ppc64le.tar.gz
[erd]: https://static.adguard.com/adguardhome/edge/AdGuardHome_darwin_arm64.zip
[erf]: https://static.adguard.com/adguardhome/edge/AdGuardHome_freebsd_arm64.tar.gz
[erl]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_arm64.tar.gz
[ero]: https://static.adguard.com/adguardhome/edge/AdGuardHome_openbsd_arm64.tar.gz
[erw]: https://static.adguard.com/adguardhome/edge/AdGuardHome_windows_arm64.zip
[exf]: https://static.adguard.com/adguardhome/edge/AdGuardHome_freebsd_386.tar.gz
[exl]: https://static.adguard.com/adguardhome/edge/AdGuardHome_linux_386.tar.gz
[exw]: https://static.adguard.com/adguardhome/edge/AdGuardHome_windows_386.zip



##  <a href="#docker" id="docker" name="docker">Docker</a>

The [official AdGuard Home Docker image][docker] is available for the following
CPU architectures:

 *  64-bit AMD;
 *  x86;
 *  64-bit ARM;
 *  ARMv6;
 *  ARMv7;
 *  64-bit PowerPC.

[docker]:  https://hub.docker.com/r/adguard/adguardhome



##  <a href="#snap" id="snap" name="snap">Snap</a>

The [official AdGuard Home Snap package][snap] is available for the following
CPU architectures:

 *  64-bit AMD;
 *  x86;
 *  64-bit ARM;
 *  ARMv7.

[snap]:       https://snapcraft.io/adguard-home
