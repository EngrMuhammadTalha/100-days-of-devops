# Day 2: Temporary User Setup with Expiry

**Task:** Create a user named `kareem` on App Server 3 with an account expiry date of 2026-12-07.

## Steps

Connect to App Server 3:

```bash
ssh banner@stapp03
sudo su -
```

Create the user with an expiry date:

```bash
useradd -e 2026-12-07 kareem
```

Verify the expiry date is set correctly:

```bash
chage -l kareem
```

**Result:** The account is automatically disabled after the specified date — useful for managing temporary or contractor access without manual follow-up.
