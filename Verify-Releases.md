 #  Verifying AdGuard Home Releases

Since AdGuard Home v0.103.4 we sign the executable files we build so that you
can verify they are really created by us and no one else.  Inside an archive
file there's a small file with `.sig` extension which contains the signature
data.  In a hypothetic situation when the binary file inside an archive is
replaced by someone, you'll know that it isn't an official release from AdGuard.


##  How to verify that the executable file was built by AdGuard?

1.  Unpack the AdGuard Home archive file.

1.  Import AdGuard Home public key from keyserver.  For **current releases,**
    run:

    ```sh
    gpg --keyserver 'keys.openpgp.org' --recv-key '28645AC9776EC4C00BCE2AFC0FE641E7235E2EC6'
    ```

    **NOTE:** For releases **before v0.107.10 and v0.108.0-b.11,** we used a key
    with the fingerprint `1222CCA0` stored on `pgp.key-server.io`.  That key is
    now invalid, and the server does not respond.

    The above command will print something similar to:

    ```none
    gpg: key 0FE641E7235E2EC6: public key "AdGuard <devteam@adguard.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    ```

1.  Verify.  On unix:

    ```sh
    gpg --verify AdGuardHome/AdGuardHome.sig
    ```

    On Windows (you might need to install PGP):

    ```ps1
    gpg --verify AdGuardHome/AdGuardHome.exe.sig
    ```

    You'll see something like this:

    ```none
    gpg: assuming signed data in 'AdGuardHome/AdGuardHome'
    gpg: Signature made Mon 15 Aug 2022 19:30:55 MSK
    gpg:                using RSA key 28645AC9776EC4C00BCE2AFC0FE641E7235E2EC6
    gpg:                issuer "devteam@adguard.com"
    gpg: Good signature from "AdGuard <devteam@adguard.com>" [ultimate]
    ```

    Check the following:

     *  RSA key: must be `28645AC9776EC4C00BCE2AFC0FE641E7235E2EC6`;
     *  issuer name: must be `AdGuard`;
     *  E-mail address: must be `devteam@adguard.com`;

    There may also be the following warning:

    ```none
    gpg: WARNING: The key's User ID is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: 2864 5AC9 776E C4C0 0BCE  2AFC 0FE6 41E7 235E 2EC6
    ```



##  Reproducing AdGuard Home builds

AdGuard Home uses [reproducible builds][repr].  See the “`build-release.sh`”
section in our [build script][build] documentation.

[build]: https://github.com/AdguardTeam/AdGuardHome/tree/master/scripts
[repr]:  https://reproducible-builds.org/
