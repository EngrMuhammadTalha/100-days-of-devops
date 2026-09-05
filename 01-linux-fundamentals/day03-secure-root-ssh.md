# Day 3: Secure Root SSH Access

**Task:** Disable direct SSH root login on all app servers in the Stratos Datacenter.

## Steps

Connect to App Server 1:

ssh tony@stapp01

sudo su -


Edit the SSH daemon config:

vi /etc/ssh/sshd_config


Find:

#PermitRootLogin yes

Change to:

PermitRootLogin no


Restart the SSH service:

systemctl restart sshd


Verify:

grep "^PermitRootLogin" /etc/ssh/sshd_config

Expected: `PermitRootLogin no`

Repeat the same steps on App Server 2 and App Server 3 to enforce this across all app servers.
