# Day 12: Linux Network Services

**Task:** Apache on one app server is unreachable on port 3001. Diagnose whether the cause is the service, the firewall, or something else, and fix it so `curl http://stapp01:3001` works from the jump host — without altering `index.html` or over-opening the firewall.

## Steps

From the jump host, confirm the problem exists:

```bash
curl http://stapp01:3001
```

Connect to App Server 1:

```bash
ssh tony@stapp01
sudo su -
```

Check whether Apache is even running — this is always the first thing to check:

```bash
systemctl status httpd
```

If it's stopped, start and enable it:

```bash
systemctl start httpd
systemctl enable httpd
```

Check what port Apache is actually listening on:

```bash
ss -tlnp | grep 3001
```

Check the configured port in Apache's config file:

```bash
grep -n "^Listen" /etc/httpd/conf/httpd.conf
```

If it shows `Listen 80` instead of `Listen 3001`, edit the file and change it, then restart:

```bash
vi /etc/httpd/conf/httpd.conf
systemctl restart httpd
```

Check whether the firewall is blocking the port:

```bash
firewall-cmd --list-ports
```

If `3001/tcp` isn't listed, open only that specific port — not the whole firewall, since the task requires not compromising security:

```bash
firewall-cmd --permanent --add-port=3001/tcp
firewall-cmd --reload
```

If Apache still won't respond, check SELinux, since it can block a service from binding to a non-standard port:

```bash
getenforce
semanage port -l | grep http_port_t
```

If port 3001 isn't in that list, add it:

```bash
semanage port -a -t http_port_t -p tcp 3001
```

Restart Apache once more:

```bash
systemctl restart httpd
```

Test locally first:

```bash
curl http://localhost:3001
```

Then test from the jump host:

```bash
curl http://stapp01:3001
```

**Result:** Diagnosed the issue step by step (service status → port config → firewall → SELinux) rather than guessing, and confirmed Apache reachable on port 3001 from the jump host with only that single port opened.
