# Day 2: Temporary User Setup with Expiry

**Task:** Create a user named `kareem` on App Server 3 with an account expiry date of 2026-12-07.

## Steps

Connect to App Server 3:

ssh banner@stapp03

Switch to root:

sudo su -


Create the user with an expiry date:

useradd -e 2026-12-07 kareem


Verify the expiry date is set correctly:

chage -l kareem


This ensures the account is automatically disabled after the specified date — useful for temporary access.
