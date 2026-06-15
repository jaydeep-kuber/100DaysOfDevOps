# Day 6: Cron Job Setup with Cronie

## Objective

Install the `cronie` package on all **Nautilus App Servers** in the **Stratos Datacenter**, start the `crond` service, and configure a sample cron job for the `root` user.

---

## Scenario

The xFusionCorp Industries Nautilus system admins team has prepared scripts to automate several day-to-day tasks. Before deploying them on a schedule, they want to test similar functionality using a sample cron job across all app servers.

---

## Task Requirements

- Install `cronie` package on all Nautilus app servers.
- Start and enable the `crond` service.
- Add the following cron job for the `root` user:

```bash
*/5 * * * * echo hello > /tmp/cron_text
```

---

## Solution

There are two ways to complete the task:
1. Normal life solution
2. Mentos life solution

### Normal Life Solution Steps:

**Simple**: SSH into each server and run the commands manually (ye hai aam zindagi):

```bash
# Step 1: SSH into the app server
ssh <user>@<app_server>

# Step 2: Install cronie
yum install -y cronie        # For RHEL/CentOS based servers
# OR
apt install -y cronie        # For Debian/Ubuntu based servers

# Step 3: Start and enable the crond service
systemctl start crond
systemctl enable crond

# Step 4: Add cron job for root user
crontab -e
# Then add this line:
*/5 * * * * echo hello > /tmp/cron_text
```

### Mentos Life Solution Steps:

**Very simple**: Do it all remotely without an interactive session (ye hai mentos zindagi):

```bash
ssh user@app-server-1 "yum install -y cronie && systemctl start crond && systemctl enable crond && (crontab -l 2>/dev/null; echo '*/5 * * * * echo hello > /tmp/cron_text') | crontab -"
```

You can choose either of the above methods to complete the task.

---

## Cron Expression Breakdown

### Expression: `*/5 * * * *`

| Field        | Value | Meaning              |
|--------------|-------|----------------------|
| Minute       | */5   | Every 5 minutes      |
| Hour         | *     | Every hour           |
| Day of Month | *     | Every day            |
| Month        | *     | Every month          |
| Day of Week  | *     | Every day of week    |

### What the job does:

```bash
echo hello > /tmp/cron_text
```

- Runs every 5 minutes.
- Writes the word `hello` into `/tmp/cron_text`.
- The `>` operator overwrites the file each time (not append).

---

## Verification

### Check crond service status:

```bash
systemctl status crond
```

Expected output (key line):

```text
Active: active (running)
```

### Check root's crontab:

```bash
crontab -l
```

Expected output:

```text
*/5 * * * * echo hello > /tmp/cron_text
```

### After 5 minutes, verify the output file:

```bash
cat /tmp/cron_text
```

Expected output:

```text
hello
```

---

## Why Cronie?

- `cronie` is the standard cron daemon for RHEL/CentOS/Rocky Linux-based systems.
- It provides the `crond` service that reads and executes scheduled jobs.
- It supports per-user crontabs (`crontab -e`) as well as system-wide `/etc/cron.d/` entries.

---

## Summary

| Action                          | Command                                                                 |
|---------------------------------|-------------------------------------------------------------------------|
| Install cronie                  | `yum install -y cronie`                                                 |
| Start crond service             | `systemctl start crond`                                                 |
| Enable crond on boot            | `systemctl enable crond`                                                |
| Add cron job for root           | `(crontab -l 2>/dev/null; echo '*/5 * * * * echo hello > /tmp/cron_text') \| crontab -` |
| Verify crontab                  | `crontab -l`                                                            |
| Check service status            | `systemctl status crond`                                                |
| Verify cron output after 5 min  | `cat /tmp/cron_text`                                                    |

---

## Key Takeaway

Using:

```bash
systemctl start crond && systemctl enable crond
```

ensures the service is:

✅ Running immediately after the command.  
✅ Automatically started on every system reboot.

And the cron entry:

```bash
*/5 * * * * echo hello > /tmp/cron_text
```

ensures that:

✅ The job runs every 5 minutes automatically.  
✅ The output is written to `/tmp/cron_text`.  
✅ Only `root` has this scheduled job in their crontab.