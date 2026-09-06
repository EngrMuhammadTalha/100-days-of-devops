# Day 7: Linux SSH Authentication

**Task:** Set up password-less SSH from user `thor` on the jump host to all app servers, through their respective sudo users (e.g. `tony` on App Server 1).

## Steps

Log in to the jump host:

```bash
ssh thor@jump-host
```

Check if an SSH key already exists:

```bash
ls -la ~/.ssh
```

If not, generate one (press Enter through all prompts):

```bash
ssh-keygen -t rsa
```

Copy the public key to App Server 1:

```bash
ssh-copy-id tony@stapp01
```

Copy the public key to App Server 2:

```bash
ssh-copy-id steve@stapp02
```

Copy the public key to App Server 3:

```bash
ssh-copy-id banner@stapp03
```

Verify password-less login works on each server:

```bash
ssh tony@stapp01
```

```bash
ssh steve@stapp02
```

```bash
ssh banner@stapp03
```

Each of these should log in without asking for a password.

If `ssh-copy-id` isn't available on the system, the key can be copied manually instead:

```bash
cat ~/.ssh/id_rsa.pub | ssh tony@stapp01 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

**Result:** `thor` can SSH into all three app servers without being prompted for a password — required for the jump host's scheduled scripts to run unattended.
