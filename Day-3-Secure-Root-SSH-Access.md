# Day-3: Secure Root SSH Access

## 📝 Task Description

Following a recent security audit, the xFusionCorp Industries security team has introduced stricter access control policies. As part of these changes, direct SSH login using the **root** account must be disabled on all application servers within the Stratos Datacenter.

**Requirements:**

* **Target Servers:**

  * `stapp01`
  * `stapp02`
  * `stapp03`
* **Security Policy:** Disable direct root SSH login.

---

## 🎯 Task Breakdown

To comply with the new security requirements, we will:

1. **Connect** to each application server.
2. **Gain administrative privileges** using sudo.
3. **Update the SSH daemon configuration** to prohibit direct root login.
4. **Validate the configuration** to avoid service disruption.
5. **Restart the SSH service** to apply changes.

---

## 🛠️ Detailed Solution & Command Breakdown

### Step 1: Connect to the Application Servers

Authenticate to each server using the provided credentials:

```bash
ssh <user>@<host>
```

---

### Step 2: Switch to Root User

Obtain administrative privileges before modifying system configuration files:

```bash
sudo su -
```

---

### Step 3: Edit SSH Configuration

Open the SSH daemon configuration file:

```bash
vi /etc/ssh/sshd_config
```

Locate the following directive:

```bash
PermitRootLogin
```

Update it to:

```bash
PermitRootLogin no
```

If the directive does not already exist, add it manually anywhere within the configuration file.

---

### Step 4: Validate the Configuration (Recommended)

Before restarting the service, verify that the configuration syntax is valid:

```bash
sshd -t
```

**Expected Result:**
No output should be displayed. Any syntax errors will be reported immediately.

---

### Step 5: Restart the SSH Service

Apply the configuration changes:

```bash
systemctl restart sshd
```

---

### Step 6: Verify the Change

Confirm that the configuration has been applied correctly:

```bash
grep "^PermitRootLogin" /etc/ssh/sshd_config
```

**Expected Output:**

```bash
PermitRootLogin no
```

You can also inspect the effective SSH configuration:

```bash
sshd -T | grep permitrootlogin
```

**Expected Output:**

```bash
permitrootlogin no
```

---

## 🔒 Security Insight

Disabling direct root SSH access is a fundamental hardening measure that aligns with the **Principle of Least Privilege (PoLP)**.

### Benefits

* Reduces exposure to brute-force attacks targeting the root account.
* Improves accountability by requiring administrators to log in with individual user accounts before elevating privileges.
* Creates clearer audit trails for security investigations.
* Prevents attackers from gaining immediate full-system access even if a user credential is compromised.

### Recommended Best Practice

Instead of logging in directly as root:

```bash
ssh username@server
sudo su -
```

This approach ensures all privileged actions are traceable to a specific user account.

---

## 🚀 Extra Assignment: SSH Hardening Checklist

After disabling root login, consider implementing additional SSH security controls:

### Disable Password Authentication

```bash
PasswordAuthentication no
```

### Restrict SSH Access to Specific Users

```bash
AllowUsers devops adminuser
```

### Change Default SSH Port (Optional)

```bash
Port 2222
```
