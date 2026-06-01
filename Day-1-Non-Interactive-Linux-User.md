# Day 1: Linux User Setup with Non-Interactive Shell.

## Task: 

To accommodate the backup agent tool's specifications, the system admin team at `xFusionCorp` Industries requires the creation of a user with a non-interactive shell. Here's your task:

Create a user named `jay` with a non-interactive shell on `App Server 1`.

## Task Breakdown
**Objective:** Create a dedicated service account (`jay`) on App Server 1 to support a backup agent tool. <br>
**Security Requirement:** The user must be configured with a non-interactive shell to prevent unauthorized console or SSH access, adhering to the principle of least privilege.<br>
**Context:** Service accounts require system presence for file ownership and background process execution, but providing them with standard interactive shells (like `/bin/bash`) creates unnecessary security vulnerabilities.

---

## Solution Breakdown

To fulfill this requirement, we will access the target application server and provision the user using the standard Linux `useradd` utility. By explicitly passing `/sbin/nologin` as the default login shell, the system will permit the account to function as a service user while actively rejecting any direct interactive login attempts. 

### Execution & Command Breakdown

#### 1. Access the Target Server
First, establish an SSH connection to App Server 1 using your authorized credentials.

```bash
ssh <user>@<host>
```

* **`ssh`**: Initiates the secure shell protocol. (Adjust credentials based on your specific lab or environment variables).

#### 2. Provision the Service Account

Execute the user creation command with elevated privileges.

```bash
sudo useradd -s /sbin/nologin jay
```

* **`sudo`**: Escalates privileges to root, which is mandatory for system account management.
* **`useradd`**: The standard administrative utility used to create a new user profile.
* **`-s /sbin/nologin`**: The critical flag that defines the user's login shell. `/sbin/nologin` actively rejects login attempts and logs a polite rejection message ("This account is currently not available") before dropping the connection. This is generally preferred for production auditing over `/bin/false`, which fails silently.
* **`jay`**: The requested username for the backup agent service account.

#### 3. Verify the Configuration

Always validate state changes to ensure the automated tool will not fail during deployment. Check the system's local user database to confirm the shell assignment.

```bash
grep jay /etc/passwd
```

* **`grep jay`**: Filters the file output to only display the configuration line matching our new user.
* **`/etc/passwd`**: The central system database file that stores user account attributes, home directories, and shell assignments.

**Expected Output:**

```text
ammar:xx:1001:1001::/home/jay:/sbin/nologin
```

*Verification check: Ensure the final colon-separated field in the output explicitly shows `/sbin/nologin`.*

---

## Security Best Practice Takeaway

When provisioning infrastructure for automated tools (CI/CD runners, monitoring agents, or backup daemons), never grant interactive terminal access. Utilizing `/sbin/nologin` ensures the tool can operate smoothly while significantly reducing the server's attack surface.

> Note: You can use `sudo useradd -s /bin/false <user>` command to archive same result, but the behavior of the both commands are different.

