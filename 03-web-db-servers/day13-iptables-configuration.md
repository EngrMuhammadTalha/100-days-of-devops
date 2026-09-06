# Day 13: IPtables Installation and Configuration

**Task:** Apache's port 3002 is currently open to everyone with no firewall in place. Install `iptables` on all app servers, block incoming traffic to port 3002 for everyone except the load balancer, and make sure the rule survives a reboot.

## Steps

Repeat the following on all three app servers (`stapp01`, `stapp02`, `stapp03`). Connect and become root:

```bash
ssh tony@stapp01
sudo su -
```

Install iptables and the systemd service that lets its rules persist across reboots:

```bash
yum install -y iptables iptables-services
```

Find the load balancer's IP address, since only it should be allowed through:

```bash
getent hosts stlb01
```

Allow the load balancer to reach port 3002 (replace `<LB_IP>` with the IP from the previous step):

```bash
iptables -I INPUT -p tcp -s <LB_IP> --dport 3002 -j ACCEPT
```

`-I` inserts this rule at the top of the list, so it's checked before the block rule below.

Block everyone else from reaching port 3002:

```bash
iptables -A INPUT -p tcp --dport 3002 -j DROP
```

Save the rules so they aren't lost on reboot:

```bash
service iptables save
```

If that command isn't available, save manually instead:

```bash
iptables-save > /etc/sysconfig/iptables
```

Enable the iptables service so saved rules load automatically at boot:

```bash
systemctl enable iptables
systemctl restart iptables
```

Verify the rules are in place and in the correct order:

```bash
iptables -L INPUT -n --line-numbers
```

You should see the ACCEPT rule for the load balancer's IP listed above the DROP rule for everyone else.

Repeat the exact same sequence on the remaining two app servers.

**Result:** `iptables` installed on all three app servers, port 3002 restricted to only the load balancer's IP, and the rules made persistent across reboots via the `iptables` service.
