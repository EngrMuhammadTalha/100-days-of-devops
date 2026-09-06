# Day 4: Script Execution Permissions

**Task:** Grant executable permissions to `/tmp/xfusioncorp.sh` on App Server 1, so all users can execute it.

## Steps

Connect to App Server 1:

```bash
ssh tony@stapp01
sudo su -
```

Check current permissions:

```bash
ls -l /tmp/xfusioncorp.sh
```

Grant execute permission to owner, group, and others:

```bash
chmod 755 /tmp/xfusioncorp.sh
```

Verify:

```bash
ls -l /tmp/xfusioncorp.sh
```

**Result:** `755` gives the owner read/write/execute, and group/others read/execute — satisfying the requirement that all users can execute the script.
