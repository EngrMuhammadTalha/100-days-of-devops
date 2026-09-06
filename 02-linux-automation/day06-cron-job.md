Day 6: Create a Cron Job

Task: Install the cronie package and start crond on all Nautilus app servers. Add a cron job for the root user that runs every 5 minutes: */5 * * * * echo hello > /tmp/cron_text.

Steps

Connect to the jump host, then repeat the following on each app server (stapp01, stapp02, stapp03):

bash
ssh tony@stapp01
sudo su -

Install cronie and start the service:

bash
yum install -y cronie
systemctl enable --now crond

Add the cron job for root:

bash
(crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | crontab -

Verify the cron entry and service status:

bash
crontab -l
systemctl is-active crond

After ~5 minutes, confirm the job ran:

bash
cat /tmp/cron_text

Result: cronie installed and crond active on all three app servers, each with the root cron job configured and confirmed working.
