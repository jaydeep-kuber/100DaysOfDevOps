# Day 5: SELinux Installation and Configuration

## 📝 Task Description

Following a recent security audit, the xFusionCorp Industries security team has opted to enhance application and server security with **SELinux**. To initiate testing, the following requirements have been established for **App Server 3** in the **Stratos Datacenter**.

---

## 🎯 Task Requirements

1. Install the required **SELinux packages**.
2. **Permanently disable** SELinux for the time being — it will be re-enabled after necessary configuration changes.
3. **No reboot required** — a scheduled maintenance reboot is already planned for tonight.
4. Disregard the current SELinux status via the command line; the **final status after the reboot should be `disabled`**.

---

## 🛠️ Detailed Solution & Command Breakdown

### Step 1: Connect to App Server 3

```bash
ssh <user>@stapp03
```

---

### Step 2: Switch to Root User

```bash
sudo su -
```

---

### Step 3: Install Required SELinux Packages

On RHEL/CentOS/AlmaLinux-based systems (standard in the Stratos Datacenter), run:

```bash
sudo yum install -y selinux-policy selinux-policy-targeted policycoreutils
```

### What each package does:

| Package | Purpose |
|---|---|
| `selinux-policy` | Core SELinux policy definitions |
| `selinux-policy-targeted` | Default targeted enforcement model |
| `policycoreutils` | Essential tools like `sestatus`, `setenforce`, `restorecon` |

---

### Step 4: Permanently Disable SELinux

Open the SELinux configuration file:

```bash
vi /etc/selinux/config
```

Locate the `SELINUX=` directive and update it to:

```bash
SELINUX=disabled
```

The final config should look like this:

```bash
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing  - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled   - No SELinux policy is loaded.
SELINUX=disabled
SELINUXTYPE=targeted
```

> ⚠️ **Important:** Editing `/etc/selinux/config` is a **permanent** change — it takes effect after the next reboot. The current runtime status will remain unchanged until then, which is expected behaviour per the task requirements.

---

### Step 5: Verify the Configuration Change

Confirm the file has been updated correctly:

```bash
grep "^SELINUX=" /etc/selinux/config
```

**Expected Output:**

```bash
SELINUX=disabled
```

---

### Step 6: Check Current Runtime Status (Informational)

```bash
sestatus
```

**Expected Output (before reboot):**

```text
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinuxtype:                    targeted
Current mode:                   enforcing
Mode from config file:          disabled
```

> 💡 Notice: **Current mode** still shows `enforcing` — this is normal. The **Mode from config file** already shows `disabled`, confirming the permanent change is in place. After tonight's maintenance reboot, `SELinux status` will show `disabled`.

---

## 🔒 Security Insight

### Why Temporarily Disable SELinux?

SELinux in `enforcing` mode can block application behaviour that hasn't been covered by an existing policy. Before enabling it in production:

* Custom policies must be written or tuned for all running services.
* File contexts (`restorecon`) must be correctly applied.
* Audit logs (`/var/log/audit/audit.log`) must be reviewed for `AVC denied` entries.

Temporarily disabling it allows teams to deploy and configure applications without security policy interference, then re-enable it with the correct policies in place.

### Modes Explained

| Mode | Behaviour |
|---|---|
| `enforcing` | SELinux is active and blocking policy violations |
| `permissive` | SELinux logs violations but does **not** block them |
| `disabled` | SELinux is completely turned off |

---

## 🚀 Extra Assignment: SELinux Re-enablement Checklist

When the team is ready to re-enable SELinux after configuration, follow this approach:

### Step 1: Set to Permissive First (Safer Approach)

```bash
# In /etc/selinux/config
SELINUX=permissive
```

Reboot, then check audit logs for any denials:

```bash
grep "AVC" /var/log/audit/audit.log
```

### Step 2: Restore File Contexts

```bash
restorecon -Rv /
```

### Step 3: Generate Custom Policy for Denials (if needed)

```bash
audit2allow -a -M mycustompolicy
semodule -i mycustompolicy.pp
```

### Step 4: Switch to Enforcing

```bash
# In /etc/selinux/config
SELINUX=enforcing
```

---

## 📋 Summary

| Action | Command |
|---|---|
| Install SELinux packages | `yum install -y selinux-policy selinux-policy-targeted policycoreutils` |
| Edit SELinux config file | `vi /etc/selinux/config` |
| Verify config change | `grep "^SELINUX=" /etc/selinux/config` |
| Check runtime status | `sestatus` |

---

## ✅ Key Takeaways

Editing `/etc/selinux/config` and setting `SELINUX=disabled`:

✅ Permanently disables SELinux after the next reboot.  
✅ Does **not** require an immediate reboot — safe for production systems with a planned maintenance window.  
✅ Keeps the system ready for future SELinux re-enablement after policy tuning.  
✅ Aligns with change management policies by avoiding unnecessary mid-cycle disruptions.