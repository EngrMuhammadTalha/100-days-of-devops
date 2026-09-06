# Day 6: Create a Cron Job

**Task:** Install the `cronie` package on all Nautilus app servers and start the `crond` service. Add a cron job for the root user that runs every 5 minutes: `*/5 * * * * echo hello > /tmp/cron_text`.

## Steps

Connect to App Server 1 from the jump host:

```bash
ssh tony@stapp01
```

Switch to root:

```bash
sudo su -
```

Install the cronie package:

```bash
yum install -y cronie
```

Enable and start the crond service:

```bash
systemctl enable --now crond
```

Add the cron job for root:

```bash
(crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | crontab -
```

Verify the cron entry:

```bash
crontab -l
```

Verify the service is running:

```bash
systemctl is-active crond
```

After about 5 minutes, confirm the job ran:

```bash
cat /tmp/cron_text
```

Repeat the same steps on App Server 2 (`steve@stapp02`) and App Server 3 (`banner@stapp03`).

**Result:** `cronie` installed and `crond` active on all three app servers, each with the root cron job configured and confirmed working.
