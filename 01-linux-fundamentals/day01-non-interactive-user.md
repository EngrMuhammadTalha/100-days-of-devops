# Day 1: Linux User Setup with Non-Interactive Shell

**Task:** Create a user named `talha` with a non-interactive shell on App Server 3.

## Steps

Connect to App Server 3 from the jump host:

```bash
ssh banner@stapp03
```

Switch to root:

```bash
sudo su -
```

Create the user with a non-interactive shell:

```bash
useradd -s /sbin/nologin talha
```

On some distributions, use `/usr/sbin/nologin` instead.

Verify:

```bash
grep '^talha:' /etc/passwd
```

**Result:** The output shows the shell set to `/sbin/nologin`, confirming the user cannot log in interactively — required for the backup agent tool's setup.
