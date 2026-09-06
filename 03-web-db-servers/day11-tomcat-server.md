# Day 11: Install and Configure Tomcat Server

**Task:** Install Tomcat on App Server 2, configure it to run on port 8089, and deploy the `ROOT.war` file (located on the jump host at `/tmp`) so the application works directly at `http://stapp02:8089`.

## Steps

From the jump host, copy the WAR file over to App Server 2:

```bash
scp /tmp/ROOT.war steve@stapp02:/tmp/
```

Connect to App Server 2 and verify the file arrived:

```bash
ssh steve@stapp02
ls -l /tmp/ROOT.war
```

Become root:

```bash
sudo su -
```

Install Tomcat:

```bash
yum install -y tomcat
```

Check the installation:

```bash
rpm -qa | grep tomcat
```

Open the Tomcat configuration to change the port:

```bash
vi /etc/tomcat/server.xml
```

Find `<Connector port="8080"` and change it to `<Connector port="8089"`, then save and exit.

Verify the change:

```bash
grep Connector /etc/tomcat/server.xml
```

Copy the WAR file into Tomcat's webapps folder:

```bash
cp /tmp/ROOT.war /var/lib/tomcat/webapps/
```

Set correct ownership — Tomcat runs as the `tomcat` user, so it needs to own its own application file:

```bash
chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war
```

Start and enable Tomcat:

```bash
systemctl enable --now tomcat
```

Confirm it's running:

```bash
systemctl status tomcat
```

Give it a few seconds, then check that the WAR file was extracted:

```bash
ls -l /var/lib/tomcat/webapps/
```
You should see both `ROOT` and `ROOT.war` — the `ROOT` folder means Tomcat successfully deployed the app.

Test the deployment:

```bash
curl http://localhost:8089
```

Or from the jump host:

```bash
curl http://stapp02:8089
```

**Result:** Tomcat installed, reconfigured to listen on port 8089, and the ROOT application deployed and reachable directly at the base URL.
