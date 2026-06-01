# Day-2: Add Temporary Linux User

## 📝 Task Description
As part of a temporary assignment to the Nautilus project, a developer named **jay** requires access to our infrastructure for a limited duration. To ensure strict access management and security compliance, a temporary user account with a hardcoded expiry date is required. 

**Requirements:**
* **Target Server:** App Server 1 (Stratos Datacenter)
* **Username:** `jay` (must be strictly in lowercase)
* **Account Expiry Date:** `2027-01-01`

---

## 🎯 Task Breakdown
To successfully complete this objective without leaving manual cleanup debt, we will:
1. **Connect** to the target application server securely via SSH.
2. **Provision** the user account utilizing the built-in system account expiration features rather than relying on calendar reminders.
3. **Verify** the system changes to ensure the expiration policy is properly attached to the user's metadata.

---

## 🛠️ Detailed Solution & Command Breakdown

### Step 1: Connect to the Target Server
First, authenticate into App Server 1 in the Stratos Datacenter. Replace `<your_admin_user>` and `<app_server_1_ip>` with your actual lab credentials (e.g., `thor@stapp01`).

```bash
ssh <your_admin_user>@<app_server_1_ip>
```

### Step 2: Provision the Temporary User

Once logged in, we use the `useradd` utility. We pass the `-e` (expiredate) flag to set the exact date on which the user account will be disabled. The date must be in `YYYY-MM-DD` format.

```bash
sudo useradd -e 2027-02-17 jay
```

* **`sudo`**: Executes the command with elevated root privileges.
* **`useradd`**: The standard Linux utility for creating new system users.
* **`-e 2027-02-17`**: Instructs the system to automatically lock the account at midnight on February 17, 2027.
* **`jay`**: The target username in lowercase.

*(Note: Depending on your specific lab environment, you may also need to assign a password to this user using `sudo passwd jay` to allow them to log in).*

### Step 3: Verify the Configuration (Best Practice)

A good DevOps engineer always verifies their state changes. Use the `chage` (change user password expiry information) command to inspect the account's lifecycle metadata.

```bash
sudo chage -l jay
```

* **`chage`**: The utility used to view and modify user password expiry information.
* **`-l`**: Lists the current aging information for the specified user.

**Expected Output:** Look for the **"Account expires"** field in the output. It should explicitly state `Feb 17, 2027`.

---

## 🚀 Extra Assignment: Automated Stale User Cleanup

**Objective:** Write a Bash script and configure a system `cron` job to identify and lock accounts that have not logged in for 90+ days.

**Scenario:** Setting explicit expiration dates (like we did for jay) is great for contractors with known end-dates. However, full-time employees or service accounts might be abandoned without notice. To maintain a secure perimeter, you need an automated process that sweeps the system, checks the last login dates, and disables inactive accounts automatically.
