# Day 5: SELinux Installation and Configuration

**Task:** Install required SELinux packages on App Server 3 and permanently disable SELinux, without rebooting now (a maintenance reboot is scheduled for later).

## Steps

Connect to App Server 3:

```bash
ssh banner@stapp03
sudo su -
```

Install the required SELinux packages:

```bash
yum install -y selinux-policy selinux-policy-targeted policycoreutils
```

Edit the SELinux config file:

```bash
vi /etc/selinux/config
```

Find:

```
SELINUX=enforcing
```

Change to:

```
SELINUX=disabled
```

Save and exit (`Esc` then `:wq`).

Verify the config file (not the live status, since a reboot is required for the change to fully take effect):

```bash
grep "^SELINUX=" /etc/selinux/config
```

Expected output: `SELINUX=disabled`

**Result:** SELinux will be disabled after the next scheduled reboot, as required.
