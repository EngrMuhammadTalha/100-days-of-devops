# Day 5: SELinux Installation and Configuration

**Task:** Install required SELinux packages on App Server 3 and permanently disable SELinux (without rebooting now — a maintenance reboot is scheduled later).

## Steps

Connect to App Server 3:

ssh banner@stapp03

sudo su -

Install the required SELinux packages:

yum install -y selinux-policy selinux-policy-targeted policycoreutils

Edit the SELinux config file:

vi /etc/selinux/config

Find:

SELINUX=enforcing

Change to:

SELINUX=disabled

Save and exit (`Esc` then `:wq`).

Verify the config file (not the live status, since a reboot is required for the change to fully apply):

grep "^SELINUX=" /etc/selinux/config

Expected: `SELINUX=disabled`
