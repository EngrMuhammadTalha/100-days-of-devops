# Day 20: Configure Nginx + PHP-FPM Using Unix Socket

**Task:** On App Server 1, install Nginx configured to serve on port 8098 with document root `/var/www/html`. Install PHP-FPM 8.1 configured to use the unix socket `/var/run/php-fpm/default.sock`, and connect Nginx to PHP-FPM through that socket. The files `index.php` and `info.php` are already present under `/var/www/html` and must not be modified. Confirm with `curl http://stapp01:8098/index.php` from the jump host.

## Steps

Connect to App Server 1:

```bash
ssh tony@stapp01
```

### 1. Install Nginx

```bash
sudo yum install -y nginx
```

Enable and start it:

```bash
sudo systemctl enable --now nginx
sudo systemctl status nginx --no-pager
```

### 2. Install PHP 8.1 and php-fpm

PHP 8.1 isn't in the default RHEL repos, so the Remi repository is needed:

```bash
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum install -y yum-utils
sudo yum-config-manager --enable remi-php81
sudo yum install -y php php-fpm php-mysqlnd php-cli php-xml php-gd php-mbstring
```

Verify the installed version:

```bash
php -v
php-fpm -v
```

### 3. Configure php-fpm to use the required unix socket

Create the parent directory for the socket (the task requires this if it doesn't already exist):

```bash
sudo mkdir -p /var/run/php-fpm
sudo chown -R apache:apache /var/run/php-fpm
```

Edit the pool configuration file:

```bash
sudo vi /etc/php-fpm.d/www.conf
```

Set these directives:

```
[www]
user = apache
group = apache
listen = /var/run/php-fpm/default.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
pm = dynamic
pm.max_children = 50
pm.start_servers = 5
pm.min_spare_servers = 2
pm.max_spare_servers = 10
```

`listen.owner`/`listen.group` are set to `nginx` since that's the process that needs to connect to the socket on this system.

Restart php-fpm:

```bash
sudo systemctl enable --now php-fpm
sudo systemctl restart php-fpm
sudo systemctl status php-fpm --no-pager
```

### 4. Configure Nginx to pass PHP requests to the socket

Create a server block, e.g. `/etc/nginx/conf.d/stapp01.conf`:

```
server {
    listen 8098;
    server_name stapp01;
    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

Test the config and reload:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

If SELinux is enforcing, allow Nginx to talk to the php-fpm socket:

```bash
sudo setsebool -P httpd_can_network_connect on
sudo chcon -R -t httpd_sys_content_t /var/www/html
sudo chcon -t httpd_var_run_t /var/run/php-fpm
```

### 5. Verify from the jump host

```bash
curl -I http://stapp01:8098/index.php
curl http://stapp01:8098/index.php
```

Expected: `HTTP/200` and the PHP-generated page content. `info.php` should work the same way.

## Troubleshooting

Check php-fpm logs if something doesn't connect:

```bash
sudo journalctl -u php-fpm -n 200
```

Check Nginx logs:

```bash
tail -f /var/log/nginx/error.log
```

Confirm the socket file actually exists with the right permissions:

```bash
ls -l /var/run/php-fpm/default.sock
```

If the owner/group on the socket doesn't match what Nginx expects, either adjust `listen.owner`/`listen.group` in the php-fpm pool config, or check which user Nginx runs as in `/etc/nginx/nginx.conf`.

**Result:** Nginx serving on port 8098 with `/var/www/html` as document root, PHP-FPM 8.1 running and listening on the required unix socket, and both pre-loaded `index.php` and `info.php` reachable from the jump host — without modifying either file.
