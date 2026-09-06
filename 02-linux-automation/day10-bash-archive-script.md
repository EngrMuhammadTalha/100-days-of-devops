# Day 10: Linux Bash Scripts

**Task:** Write a script `blog_archive.sh` on App Server 1 that zips `/var/www/html/blog` into `/archives/xfusioncorp_blog.zip` and copies it to the Storage Server. No `sudo` inside the script, no password prompts during the copy, and the regular app server user (`tony`) must be able to run it.

## Steps

Connect to App Server 1:

```bash
ssh tony@stapp01
```

Become root, but only to install the one package needed — not to run the script itself:

```bash
sudo su -
```

Install zip (the tool used to create `.zip` archives):

```bash
yum install -y zip
```

Exit back to the regular user, since the task requires the script to run without root:

```bash
exit
```

Create the folder where the script will live:

```bash
mkdir -p /scripts
```

Set up password-less SSH to the Storage Server. This is needed because the script uses `scp` (secure copy) to send the file over — if SSH ever asks for a password, an automated script would just hang waiting for input:

```bash
ssh-keygen -t rsa
```

Copy the generated key to the Storage Server so it's trusted for future logins:

```bash
ssh-copy-id natasha@ststor01
```

Create the script file:

```bash
vi /scripts/blog_archive.sh
```

Script contents — `zip -r` compresses the folder recursively (including subfolders), and `scp` copies the result to the Storage Server:

```bash
#!/bin/bash

zip -r /archives/xfusioncorp_blog.zip /var/www/html/blog
scp /archives/xfusioncorp_blog.zip natasha@ststor01:/archives/
```

Make the script executable — without this, Linux won't let you run it directly:

```bash
chmod +x /scripts/blog_archive.sh
```

Run it:

```bash
/scripts/blog_archive.sh
```

## Issues I ran into and how I solved them

**Issue 1: Script asked for a password when copying**
The first time I ran the script, `scp` paused and asked for `natasha`'s password — which defeats the purpose of an automated script.

Cause: I hadn't set up SSH key authentication to the Storage Server yet.

Fix: ran `ssh-keygen -t rsa` and `ssh-copy-id natasha@ststor01` *before* running the script, so the key-based trust was already in place. After that, `scp` connected silently with no prompt.

**Issue 2: "Permission denied" when running the script**
Running `/scripts/blog_archive.sh` directly failed with a permission error.

Cause: the file didn't have execute permission yet.

Fix: `chmod +x /scripts/blog_archive.sh` — this is a common beginner trip-up, since creating a file with `vi` never makes it executable automatically.

## Verify

Confirm the archive exists locally:

```bash
ls -l /archives
```

Confirm it also landed on the Storage Server:

```bash
ssh natasha@ststor01 "ls -l /archives"
```

**Result:** A working, password-less archival script that `tony` can run directly with no `sudo` calls anywhere inside it — the zip is created locally and automatically copied to the Storage Server via SSH key authentication.

**Tip for beginners:** anytime a script needs to copy files to another server "automatically," set up SSH keys (`ssh-keygen` + `ssh-copy-id`) first — otherwise the script will stall waiting for a password that never comes in an automated context.
