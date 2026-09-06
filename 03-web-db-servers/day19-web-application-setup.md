# Day 19: Install and Configure Web Application

**Task:** Install Apache on App Server 3, configure it to serve on port 5004, and set up two static website backups (`/home/thor/media` and `/home/thor/cluster` from the jump host) so they're reachable at `http://localhost:5004/media/` and `http://localhost:5004/cluster/`.

## Steps

Connect to App Server 3:

```bash
ssh banner@stapp03
```

Install Apache:

```bash
sudo yum install httpd -y
```

Edit the Apache config to change the listening port:

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

Find `Listen 80` and change it to `Listen 5004`.

Copy the two website backups from the jump host onto this server:

```bash
scp -r thor@jump-host:/home/thor/media /tmp/
scp -r thor@jump-host:/home/thor/cluster /tmp/
```

Move them into Apache's document root:

```bash
sudo cp -r /tmp/media /var/www/html/
sudo cp -r /tmp/cluster /var/www/html/
```

This results in the structure:
```
/var/www/html/
├── media/
│   └── index.html
└── cluster/
    └── index.html
```

Enable and restart Apache to apply the new port:

```bash
sudo systemctl enable httpd
sudo systemctl restart httpd
```

Confirm Apache is listening on the new port:

```bash
ss -tlnp | grep 5004
```
Expected: `LISTEN ... :5004`

Test both sites:

```bash
curl http://localhost:5004/media/
curl http://localhost:5004/cluster/
```

Both should return their respective page content.

**Result:** Apache installed and reconfigured to serve on port 5004, with both static site backups deployed under the document root and confirmed reachable at their respective `/media/` and `/cluster/` paths.
