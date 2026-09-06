# Day 15: Setup SSL for Nginx

**Task:** Install and configure Nginx on App Server 2, deploy the provided self-signed SSL certificate (`/tmp/nautilus.crt` and `/tmp/nautilus.key`), create an `index.html` with the content "Welcome!", and confirm it's reachable over HTTPS from the jump host.

## Steps

Connect to App Server 2 and become root:

```bash
ssh steve@stapp02
sudo su -
```

Install Nginx:

```bash
yum install -y nginx
```

Move the provided certificate and key into Nginx's standard certificate locations:

```bash
mv /tmp/nautilus.crt /etc/pki/nginx/server.crt
mv /tmp/nautilus.key /etc/pki/nginx/private/server.key
```

Confirm the private key has the correct restrictive permissions (only root should be able to read it):

```bash
ls -l /etc/pki/nginx/private/server.key
```
Expected: `-rw------- 1 root root`

Create the required web page under Nginx's default document root:

```bash
echo "Welcome!" > /usr/share/nginx/html/index.html
```

Verify it:

```bash
cat /usr/share/nginx/html/index.html
```

Open the main Nginx config file:

```bash
vi /etc/nginx/nginx.conf
```

Find the commented-out SSL server block near the bottom of the file (it starts with `# server {` and `# listen 443 ssl http2;`), then uncomment it so it looks like this:

```
server {
    listen       443 ssl http2;
    listen       [::]:443 ssl http2;

    server_name  _;
    root         /usr/share/nginx/html;

    ssl_certificate "/etc/pki/nginx/server.crt";
    ssl_certificate_key "/etc/pki/nginx/private/server.key";

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout 10m;
    ssl_ciphers PROFILE=SYSTEM;
    ssl_prefer_server_ciphers on;

    include /etc/nginx/default.d/*.conf;
}
```

Test the configuration for syntax errors before restarting:

```bash
nginx -t
```
Expected: `nginx: configuration file /etc/nginx/nginx.conf test is successful`

Double-check the certificate paths are actually loaded in the config:

```bash
nginx -T | grep ssl_certificate
```

Restart Nginx to apply the changes cleanly:

```bash
systemctl restart nginx
systemctl status nginx
```
Expected: `active (running)`

Test locally first. Since the certificate is self-signed, `curl` needs `-k` to skip certificate verification:

```bash
curl -Ik -k https://localhost
```
Expected: `HTTP/2 200` with the Nginx server header.

Exit back to the jump host and confirm it's reachable from there too:

```bash
exit
curl -Ik -k https://stapp02
```
Expected: `HTTP/2 200` with the Nginx server header.

**Result:** Nginx installed, SSL certificate and key placed in the standard `/etc/pki/nginx/` locations with correct permissions, the SSL server block enabled in `nginx.conf`, and the "Welcome!" page confirmed reachable over HTTPS both locally and from the jump host.
