# Day 17: Install and Configure PostgreSQL

**Task:** PostgreSQL is already installed on the database server. Create a database user `kodekloud_cap` with password `8FmzjvFU6S`, create a database `kodekloud_db6`, and grant that user full permissions on it — without restarting the PostgreSQL service.

## Steps

Connect to the database server:

```bash
ssh peter@stdb01
```

Switch to the `postgres` system user, since PostgreSQL commands need to run as that user:

```bash
sudo su - postgres
```

Open the PostgreSQL shell:

```bash
psql
```

Create the database user with the required password:

```sql
CREATE USER kodekloud_cap WITH PASSWORD '8FmzjvFU6S';
```

Create the database:

```sql
CREATE DATABASE kodekloud_db6;
```

Grant full privileges on that database to the new user:

```sql
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db6 TO kodekloud_cap;
```

Exit the PostgreSQL shell:

```sql
\q
```

Exit back from the `postgres` user:

```bash
exit
```

## Issues I ran into and how I solved them

**Issue: `psql` failed with "password authentication failed for user root"**

Running `psql` directly while logged in as `root` failed with:
```
psql: error: FATAL: password authentication failed for user "root"
```

Cause: `psql` tries to connect using a PostgreSQL role matching the current Linux user by default. There's no PostgreSQL role for `root`, so the connection is rejected.

Fix: switch to the `postgres` system user first with `sudo su - postgres`, then run `psql` from there — this connects as the `postgres` role, which exists and has the needed privileges. (An alternative that also works: `sudo -u postgres psql`.)

## Verify

Reconnect as `postgres` and check the user and database exist:

```bash
sudo su - postgres
psql
```

```sql
\du
\l
```

`kodekloud_cap` should appear in the user list, and `kodekloud_db6` should appear in the database list.

**Result:** Database user and database created with full privileges granted, verified with `\du` and `\l`, and the PostgreSQL service was never restarted as required.
