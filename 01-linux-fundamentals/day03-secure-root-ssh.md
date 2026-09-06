# Day 3: Secure Root SSH Access

**Task:** Disable direct SSH root login on all app servers in the Stratos Datacenter.

## Steps

Connect to App Server 1:

```bash
ssh tony@stapp01
sudo su -
```

Edit the SSH daemon config:

```bash
vi /etc/ssh/sshd_config
```

Find:

```
#PermitRootLogin yes
```

Change to:

```
PermitRootLogin no
```

Restart the SSH service:

```bash
systemctl restart sshd
```

Verify:

```bash
grep "^PermitRootLogin" /etc/ssh/sshd_config
```

Expected output: `PermitRootLogin no`

**Result:** Root can no longer SSH in directly. Repeated the same steps on App Server 2 and App Server 3 to enforce this policy across all app servers.
