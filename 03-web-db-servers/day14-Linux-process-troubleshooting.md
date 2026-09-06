# Day 14: Linux Process Troubleshooting

**Task:** Apache is reported unavailable on one app server. Find the faulty server and make sure Apache is up and running on all app servers, listening on port 8086. The servers don't need to serve actual pages yet — just have the service running correctly.

## Steps

Repeat the following checks on each app server (`stapp01`, `stapp02`, `stapp03`) to identify which one is faulty:

```bash
ssh tony@stapp01
sudo su -
```

Check whether Apache is running:

```bash
systemctl status httpd
```

If it's stopped or failed, start it and enable it on boot:

```bash
systemctl start httpd
systemctl enable httpd
```

Check that Apache is actually listening on the required port:

```bash
ss -tlnp | grep 8086
```

Check the configured port inside Apache's config:

```bash
grep "^Listen" /etc/httpd/conf/httpd.conf
```

If it shows `Listen 80` instead of `Listen 8086`, edit the file:

```bash
vi /etc/httpd/conf/httpd.conf
```
Change it to `Listen 8086`, save, then restart:

```bash
systemctl restart httpd
```

If Apache isn't installed at all on a given server, install it first:

```bash
rpm -qa | grep httpd
yum install -y httpd
```

If something else is already using port 8086, identify and stop it:

```bash
lsof -i :8086
```

Validate the Apache configuration syntax before restarting, to rule out config errors:

```bash
httpd -t
```
Expected: `Syntax OK`

Confirm the final state on each server:

```bash
systemctl is-active httpd
ss -tlnp | grep 8086
```

Repeat this exact check across `stapp01`, `stapp02`, and `stapp03` — the faulty server is the one where `systemctl status httpd` shows failed, or where Apache is listening on port 80 instead of 8086.

**Result:** Identified the specific app server with the Apache issue by checking service status, listening port, and config across all three, then corrected the port and restarted so Apache runs on 8086 on every server.
