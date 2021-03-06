Unattended upgrades
===================

This script can upgrade packages automatically and unattended.  
However, it is not enabled by default.  Most users enable it via the
Software Sources program (available in System/Administration). 

If you would prefer to enable it from the command line, run 
"sudo dpkg-reconfigure -plow unattended-upgrades".

It will not install packages that require dependencies that can't be
fetched from allowed origins and it will check for conffile prompts
before the install and holds back any package that requires them. 

Setup
-----

The unattended-upgrades package is normally activated by
software-properties or via debconf. By default this runs an update
every day.

The main way to specify which packages will be auto-upgraded is by
means of their "origin" and "archive".  These are taken respectively
from the Origin and Suite fields of the respository's Release file,
or can be found in the output of:
```
$ apt-cache policy
```
in the "o" and "a" fields for the given repository.

The default setup auto-updates packages in the main and security
archives, which means that only stable and security updates are
applied.

This can be changed either with the
"Unattended-Upgrade::Allowed-Origins" or the 
"Unattended-Upgrade::Origins-Pattern" apt configuration lists, which
can be configured in /etc/apt/apt.conf.d/50unattended-upgrades.
Also in this file are a range of other options that can be configured.

Allowed-Origins is a simple list of patterns of the form
"origin:archive".

Origins-Pattern allows you to give a list of
patterns to match against.  For example:
```
 Unattended-Upgrade::Origins-Pattern {
        "origin=Google\, Inc.,suite=contrib";
        "site=www.example.com,component=main";
 };
```
will upgrade a package if either the origin is "Google, Inc." and
suite is "contrib" or if it comes from www.example.com and is in
component "main".  The apt-cache policy short identifiers
(e.g. "o" for "origin") are also supported.

All operations are be logged in /var/log/unattended-upgrades/. This
includes the dpkg output as well.

If you want mail support you need to have a mail-transport-agent (e.g
postfix) or mailx installed.

Debugging
---------

If something goes wrong or if you want to report a bug about the way
the script works its a good idea to run:
```
$ sudo unattended-upgrade --debug --dry-run
```
and look at the resulting logfile in:
/var/log/unattended-upgrades/unattended-upgrades.log 
then. It will contain additional debug information.


Manual Setup
------------

To activate this script manually you need to ensure that the apt
configuration contains the following lines (this can be done via the
graphical "Software Source" program or via dpkg-reconfigure as well):
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```
This means that it will check for upates every day and install them
(if that is possible). If you have update-notifier installed, it will
setup /etc/apt/apt.conf.d/10periodic. Just edit this file then to fit
your needs. If you do not have this file, just create it or
create/edit /etc/apt/apt.conf - you can check your configuration by
running "apt-config dump".
