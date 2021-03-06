openvpn-release-scripts
=======================

These scripts automate a large subset of the OpenVPN 2.x release process:

* **make-openvpn-release.sh**: produces tarballs, changelog and man-pages
* **sign-and-push.sh**: sign release files with GPG and push them to the secondary webserver
* **verify-openvpn-release.sh**: download release files from webservers and verify their GPG signatures

The last script is particularly important when Content Delivery systems (e.g.
CloudFlare) are in the middle and may be serving obsolete files.

Configuration
=============

Before you run these scripts:

* Ensure that you can build OpenVPN manually
* Install man2html
* Install gnupg
* Ensure that you have GPG key for signing
* Ensure that you have a clean GPG keyring available for verifying signatures

Once these are in order copy vars.example to vars and add your GPG key ID.

Usage
=====

First run

    $ ./make-openvpn-release.sh

This will produce a directory called "release" with all the release files. Note
that any previous "release" directory will be unconditionally wiped away. You
can avoid that by renaming it and running the subsequent commands against the
copy.

You can override some variables on the command-line. A typical use-case is
changing the OpenVPN versions:

    OPENVPN_PREVIOUS_VERSION=2.3.16 OPENVPN_CURRENT_VERSION=2.3.17 ./make-openvpn-release.sh

Another one is using a custom OpenVPN repository as the source:

    OPENVPN_REPO="<url>/openvpn.git" ./make-openvpn-release.sh

Refer to vars.example for all overrideable parameters.

The script will ask for your GPG key password when signing the release files.
Depending on your GnuPG agent / pinentry configuration it may be a good idea to
have the GPG password on your clipboard.

Once all the release files (tarballs, Windows installers, openvpn-gui tarball)
are present in a directory (e.g. release/sources), you can sign and push all
the files to the download servers like this:

    $ ./sign-and-push.sh release

When you've moved the release files to their correct places on the webservers 
you can automatically download the files and verify their GPG signatures:

    $ ./verify-openvpn-release.sh release

This is particularly useful with CloudFlare which has a habit of caching
obsolete files.
