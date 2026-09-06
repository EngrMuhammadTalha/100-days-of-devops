# Day 9: MariaDB Troubleshooting

**Task:** The Nautilus application in Stratos DC can't connect to its database. The `mariadb` service is down on the database server — investigate and fix it.

## Steps

Connect to the database server:

```bash
ssh peter@stdb01
```

Become root:

```bash
sudo su -
```

Check the service status first — this is always the first troubleshooting step:

```bash
systemctl status mariadb
```

Try starting it:

```bash
systemctl start mariadb
```

If it fails, check the logs for the actual error:

```bash
journalctl -u mariadb --no-pager -n 30
```

From there, check the most common causes one at a time. Check ownership of the data directory:

```bash
ls -ld /var/lib/mysql
```

Fix ownership if it's wrong:

```bash
chown -R mysql:mysql /var/lib/mysql
```

Check for disk space issues:

```bash
df -h
```

Check for a stale PID file:

```bash
ls /var/lib/mysql/*.pid
```

Remove it if the server isn't actually running:

```bash
rm -f /var/lib/mysql/*.pid
```

Once the root cause is identified and fixed, start the service:

```bash
systemctl start mariadb
```

Enable it so it starts on boot:

```bash
systemctl enable mariadb
```

Verify:

```bash
systemctl is-active mariadb
```

**Result:** Identified the underlying cause via `systemctl status` and `journalctl` rather than guessing, applied the targeted fix, and confirmed the service was active again — restoring database connectivity for the application.
