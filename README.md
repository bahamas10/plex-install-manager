plex-install-manager
====================

Manage plex media server installations (for Linux).

This program downloads Plex Linux deb files and extracts them to a given
directory - it then optionally updates an "active" symlink (with `-a`).

Example
-------

Given a basic directory structure:

    $ sudo mkdir /opt/plex /opt/plex/src /opt/plex/data

- `/opt/plex/src` - Plex media server binaries and installation
- `/opt/plex/data` - Persistent data used by the server

Install the latest version of plex and activate it:

    $ sudo ./plex-install-manager -a -d /opt/plex/src
    chdir:  /opt/plex/src
    latest:  plexmediaserver_1.24.5.5173-8dcc73a59_amd64
    download:  plexmediaserver_1.24.5.5173-8dcc73a59_amd64/pkg/plexmediaserver_1.24.5.5173-8dcc73a59_amd64.deb
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100 83.0M  100 83.0M    0     0  30.3M      0  0:00:02  0:00:02 --:--:-- 30.3M
    extract:  deb plexmediaserver_1.24.5.5173-8dcc73a59_amd64/pkg/plexmediaserver_1.24.5.5173-8dcc73a59_amd64.deb
    extract:  tar plexmediaserver_1.24.5.5173-8dcc73a59_amd64/pkg/data.tar.xz
    link:  plexmediaserver_1.24.5.5173-8dcc73a59_amd64 as active
    finish:  /opt/plex/src/plexmediaserver_1.24.5.5173-8dcc73a59_amd64
    done!

This ensures that plex is installed to `/opt/plex/src/active`.  You can verify
this with:

    $ ls -lh /opt/plex/src
    total 2.0K
    lrwxrwxrwx 1 root root 43 Nov 15 02:44 active -> plexmediaserver_1.24.5.5173-8dcc73a59_amd64/
    drwxr-xr-x 4 root root  4 May 19 15:13 plexmediaserver_1.22.3.4523-d0ce30438_amd64/
    drwxr-xr-x 4 root root  4 Jun 20 21:30 plexmediaserver_1.23.2.4656-85f0adf5b_amd64/
    drwxr-xr-x 4 root root  4 Nov 15 02:44 plexmediaserver_1.24.5.5173-8dcc73a59_amd64/

To start the server you can run something like this:

``` bash
cd /opt/plex/src/active/root/usr/lib/plexmediaserver || exit 1
export PLEX_MEDIA_SERVER_HOME=/opt/plex/src/active/root/usr/lib/plexmediaserver
export PLEX_MEDIA_SERVER_APPLICATION_SUPPORT_DIR=/opt/plex/data
exec chpst -u _plex:_plex ./Plex\ Media\ Server
```

Because of the `active` symlink you will only need to restart the Plex service
after a new update is installed for it to take effect.

Dry Run
-------

You can run with `-n` to see if an update is available:

    $ ./plex-install-manager -a -d /opt/plex/src -n
    chdir:  /opt/plex/src
    latest:  plexmediaserver_1.24.5.5173-8dcc73a59_amd64
    version already installed

Usage
-----

    $ ./plex-install-manager -h
    Usage: plex-install-manager [-afnh] [-d <dir>]

    Manage installations of Plex Media Server for Linux.

    Options:
        -a         create/set the symlink "active" to the newly installed package
        -d <dir>   the dir to work in, defaults to CWD
        -f         force installation if it already exists
        -h         print this message and exit
        -n         dry-run, defaults to false
        -t <token> optional Plex Pass token

License
-------

MIT License
