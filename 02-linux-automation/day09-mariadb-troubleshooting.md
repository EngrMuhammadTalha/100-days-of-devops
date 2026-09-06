# Day 9: MariaDB Troubleshooting

**Task:** The Nautilus application in Stratos DC can't connect to its database. The `mariadb` service is down on the database server — investigate and fix it.

## Steps

Connect to the database server:

```bash
ssh peter@stdb01
```

Become root (needed to manage services and view system logs):

```bash
sudo su -
```

Check the service status first. This is always the first troubleshooting step for any "service is down" problem — it tells you whether it's running, and often shows the error directly:

```bash
systemctl status mariadb
```

Try starting it:

```bash
systemctl start mariadb
```

If it fails, check the logs for the actual error message. `journalctl` shows recent system logs for a specific service:

```bash
journalctl -u mariadb --no-pager -n 30
```

## Issues I ran into and how I solved them

**Issue 1: Wrong ownership on the data directory**
MariaDB stores its data in `/var/lib/mysql`, and that folder must be owned by the `mysql` user — if it's owned by someone else (e.g. `root`), the service refuses to start.

Check ownership:
```bash
ls -ld /var/lib/mysql
```
Fix it:
```bash
chown -R mysql:mysql /var/lib/mysql
```

**Issue 2: Disk full**
If the server's disk is full, MariaDB can't write its files and won't start.

Check disk usage:
```bash
df -h
```
If a partition is at 100%, free up space (e.g. delete old logs) before retrying.

**Issue 3: Leftover PID file**
Sometimes a `.pid` file is left behind from a previous crash, and MariaDB thinks it's already running when it isn't.

Check for it:
```bash
ls /var/lib/mysql/*.pid
```
Remove it if the server isn't actually running:
```bash
rm -f /var/lib/mysql/*.pid
```

## After the fix

Once the actual cause is found and corrected, start the service:

```bash
systemctl start mariadb
```

Enable it so it also starts automatically after a reboot:

```bash
systemctl enable mariadb
```

Confirm it's running:

```bash
systemctl is-active mariadb
```
Expected output: `active`

**Result:** Rather than guessing, I used `systemctl status` and `journalctl` to find the real error first, then applied the specific fix for that cause. This restored database connectivity for the Nautilus application.

**Tip for beginners:** this same pattern (`systemctl status` → `journalctl -u <service>` → read the error → fix only that → restart → verify with `systemctl is-active`) works for troubleshooting almost any Linux service, not just MariaDB.
