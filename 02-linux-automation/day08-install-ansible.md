# Day 8: Install Ansible

**Task:** Install Ansible version 4.9.0 on the jump host using `pip3` only. Make sure the Ansible binary is available globally, so all users on the system can run Ansible commands.

## Steps

Log in to the jump host:

```bash
ssh thor@jump-host
```

Become root:

```bash
sudo su -
```

Confirm pip3 is available:

```bash
pip3 --version
```

Install the exact required version using pip3 only:

```bash
pip3 install ansible==4.9.0
```

Find where pip installed the binary:

```bash
which ansible
```

If this returns nothing, search for it directly:

```bash
find / -name ansible 2>/dev/null
```

If it was installed under a root-only path like `/root/.local/bin`, symlink it into `/usr/bin` so every user can run it:

```bash
ln -s /root/.local/bin/ansible /usr/bin/ansible
```

Verify:

```bash
ansible --version
```

**Result:** Ansible 4.9.0 installed via pip3 and callable by any user on the system, confirming the jump host is ready to act as the Ansible controller.
