# Day 18: Install and Configure DB Server

**Task:** Install and configure MariaDB on the database server, create a database `kodekloud_db6`, create a user `kodekloud_joy` with password `dCV3szSGNA`, and grant that user full permissions on the database.

## Steps

Connect to the database server:

```bash
ssh peter@stdb01
```

Install MariaDB:

```bash
sudo yum install mariadb-server -y
```

Start the service:

```bash
sudo systemctl start mariadb
```

Enable it on boot:

```bash
sudo systemctl enable mariadb
```

Check it's running:

```bash
sudo systemctl status mariadb
```
Expected: `active (running)`

Optionally secure the installation (sets root password, removes anonymous users and the test database):

```bash
sudo mysql_secure_installation
```

Log in to MariaDB:

```bash
mysql -u root -p
```

Create the database:

```sql
CREATE DATABASE kodekloud_db6;
```

Verify it exists:

```sql
SHOW DATABASES;
```

Create the user with the required password:

```sql
CREATE USER 'kodekloud_joy'@'localhost' IDENTIFIED BY 'dCV3szSGNA';
```

Verify the user was created:

```sql
SELECT user,host FROM mysql.user;
```

Grant full permissions on the database to that user:

```sql
GRANT ALL PRIVILEGES ON kodekloud_db6.* TO 'kodekloud_joy'@'localhost';
```

Apply the privilege changes:

```sql
FLUSH PRIVILEGES;
```

Exit:

```sql
EXIT;
```

## Verify

Log in as the new user to confirm access:

```bash
mysql -u kodekloud_joy -p
```

```sql
SHOW DATABASES;
```
Expected: `kodekloud_db6` appears in the list.

**Result:** MariaDB installed and running, database and user created, and full privileges granted — confirmed by logging in as the new user and seeing the database listed.
