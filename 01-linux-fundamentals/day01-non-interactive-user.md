# Day 1: Linux User Setup with Non-Interactive Shell

**Task:** Create a user named `talha` with a non-interactive shell on App Server 3.

## Steps

Connect to App Server 3 from the jump host:

ssh banner@stapp03


Switch to root:

sudo su -


Create the user with a non-interactive shell:

useradd -s /sbin/nologin talha

(On some distributions, use `/usr/sbin/nologin` instead.)

Verify:

grep '^talha:' /etc/passwd


Expected output shows the shell set to `/sbin/nologin`, confirming the user cannot log in interactively.
