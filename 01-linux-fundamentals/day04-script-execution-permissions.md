# Day 4: Script Execution Permissions

**Task:** Grant executable permissions to `/tmp/xfusioncorp.sh` on App Server 1, so all users can execute it.

## Steps

Connect to App Server 1:

ssh tony@stapp01

sudo su -


Check current permissions:

ls -l /tmp/xfusioncorp.sh

Grant execute permission to owner, group, and others:

chmod 755 /tmp/xfusioncorp.sh

Verify:

ls -l /tmp/xfusioncorp.sh

`755` means the owner can read/write/execute, while group and others can read/execute — satisfying "all users can execute it."
