# Day 16: Install and Configure Nginx as an LBR

**Task:** Configure the load balancer server with Nginx so it distributes traffic across all three app servers, using only the main `/etc/nginx/nginx.conf` file — without changing Apache's existing port on the app servers. Confirm it works via `curl http://stlb01:80`.

## Steps

Connect to the load balancer server:

```bash
ssh loki@stlb01
```

Install Nginx if it isn't already installed:

```bash
sudo yum install -y nginx
```

Verify the installation:

```bash
nginx -v
```

Edit the main Nginx configuration file:

```bash
sudo vi /etc/nginx/nginx.conf
```

Inside the existing `http {}` block, add an upstream group listing all three app servers, and a server block that proxies requests to that group:

```
upstream nautilus_app {
    server stapp01:80;
    server stapp02:80;
    server stapp03:80;
}

server {
    listen 80;

    location / {
        proxy_pass http://nautilus_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Nothing on the app servers themselves needs to change — they keep listening on port 80 as before.

Test the configuration for syntax errors:

```bash
sudo nginx -t
```
Expected: `syntax is ok` / `test is successful`

Enable and restart Nginx:

```bash
sudo systemctl enable nginx
sudo systemctl restart nginx
sudo systemctl status nginx
```

Confirm Apache is running on all three app servers (required by the task, even though nothing there was changed):

```bash
ssh tony@stapp01
sudo systemctl status httpd
exit
```
Repeat the same check for `steve@stapp02` and `banner@stapp03`. If any is stopped:

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

Test the load balancer:

```bash
curl http://stlb01:80
```

**Result:** Nginx installed and configured as a load balancer using only `nginx.conf`, distributing traffic across all three app servers via an `upstream` block, with Apache confirmed running unchanged on each app server and the site reachable through the load balancer.
