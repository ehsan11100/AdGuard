# Verifying AdGuard Home Releases

Since AdGuard Home v0.103.4 we sign the executable files we build so you can verify they are really created by us and no one else.
Inside an archive file there's a small file with `.sig` extension which contains the signature data.
In a hypothetic situation when the binary file inside an archive is replaced by someone, you'll know that it isn't an official release from AdGuard.


## How to verify that the executable file was built by AdGuard?

1. Unpack AdGuard Home archive file
2. Import AdGuard Home public key from keyserver:

        gpg --keyserver pgp.key-server.io --recv-key 1222CCA0

    The above command will print:

        gpg: key 9A6F0EB91222CCA0: public key "AdGuard <devteam@adguard.com>" imported

3. Verify (for UNIX):

        gpg --verify AdGuardHome/AdGuardHome.sig

    or for Windows:

        gpg --verify AdGuardHome/AdGuardHome.exe.sig

    You'll see something like this:

        gpg: assuming signed data in 'AdGuardHome/AdGuardHome'
        gpg: Signature made Wed 12 Aug 2020 11:06:44 PM +03
        gpg:                using RSA key 58D6AD46BC509C6181A22C5F9A6F0EB91222CCA0
        gpg:                issuer "devteam@adguard.com"
        gpg: Good signature from "AdGuard <devteam@adguard.com>" [unknown]

    Check:
    * RSA key - must be "58D6AD46BC509C6181A22C5F9A6F0EB91222CCA0"
    * name - must be "AdGuard"
    * email address - must be "devteam@adguard.com"

> Note Windows users: you need to install PGP software.


## Reproducing AdGuard Home releases

    git clone --branch "v0.103.3" --depth=1 "https://github.com/AdguardTeam/AdGuardHome" /tmp/adguard
    cd /tmp/adguard
    docker run --volume $(pwd):/build --rm --env CHANNEL=release --net=host adguard/golang-ubuntu make release

where "v0.103.3" is the AdGuard Home version you want to build.
