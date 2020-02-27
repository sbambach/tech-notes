# Using NFS with vagrant on windows

## Description

NFS is an distributed network filesystem implemented by Sun in 80's. Today it's often used to connect multiple clients to a unix based shared network drive.

## Prerequisite

1) Install vagrant-winnfsd (1.4.0) with `vagrant plugin install vagrant-winnfsd`
2) Switch vagrant to use NFS as described [here](https://www.vagrantup.com/docs/synced-folders/nfs.html).

## Windows firewall blocks NFS traffic, so let's fix that

1) Open `cmd` as administrator. See also [here](https://www.howtogeek.com/194041/how-to-open-the-command-prompt-as-administrator-in-windows-8.1/).
2) Change to your `~/vagrant-box` directory
3) Delete obsolete firewall rule with (used by old winnfsd plugins?)

    ```bash
    netsh advfirewall firewall show rule name="winnfsd.exe"
    ```

4) Call `vagrant status`
5) If it complains about...

    ```bash
    It seems that you don't have the privileges to change the firewall rules. NFS will not work without that firewall
    changes. Execute the following commands via cmd as administrator:
    netsh advfirewall firewall add rule...
    ```

6) ... just do it! Execute the lines in your administrator console.
7) Test, if new rule exists

    ```bash
    $ netsh advfirewall firewall show rule name="VagrantWinNFSd-1.4.0"

    Regelname:                               VagrantWinNFSd-1.4.0
    ----------------------------------------------------------------------
    Aktiviert:                               Ja
    Richtung:                                Aus
    Profile:                                 Domäne,Privat,Öffentlich
    Gruppierung:
    Lokales IP:                              Beliebig
    Remote-IP:                               Beliebig
    Protokoll:                               Beliebig
    Edgeausnahme:                            Nein
    Aktion:                                  Zulassen

    Regelname:                               VagrantWinNFSd-1.4.0
    ----------------------------------------------------------------------
    Aktiviert:                               Ja
    Richtung:                                Eingehend
    Profile:                                 Domäne,Privat,Öffentlich
    Gruppierung:
    Lokales IP:                              Beliebig
    Remote-IP:                               Beliebig
    Protokoll:                               Beliebig
    Edgeausnahme:                            Nein
    Aktion:                                  Zulassen
    OK.
    ```

    Sorry for german output... Are there any rules, than it should be ok.

8) Test, if obsolete rule was removed

    ```bash
    $ netsh advfirewall firewall show rule name="winnfsd.exe"

    Keine Regeln stimmen mit den angegebenen Kriterien überein.
    ```

    Sorry for german output... No rules shown is fine. If there are any, delete them!

## Now you should be ready to start your VM

```bash
$ vagrant halt # just in case old box is already running
$ vagrant up

...
==> default: Exporting NFS shared folders...
==> default: Preparing to edit nfs mounting file.
[NFS] Status: running
==> default: Mounting NFS shared folders...
...
```

Done.