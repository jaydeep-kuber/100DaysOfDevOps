# Day 4: Script Execution Permissions

## Objective

Grant executable permissions to the `xfusioncorp.sh` script located on **App Server 1** within the **Stratos Datacenter**, ensuring that all users can execute the script.

---

## Scenario

The xFusionCorp Industries system administration team has developed a Bash script named:

```bash
/tmp/xfusioncorp.sh
```

The script has been distributed across the infrastructure, but it currently lacks executable permissions on **App Server 1**.

To enable automation processes and allow users to run the script, appropriate file permissions must be configured.

---

## Task Requirements

- Grant executable permission to the script.
- Ensure that:
  - The owner can read, write, and execute the script.
  - Group members can read and execute the script.
  - Other users can read and execute the script.

---

## Solution
There are two ways to complete the task: 
1. Normal life solution
2. Mentos life solution

### Normal Life solution steps:

**Simple**: Do ssh on server and execute the following command ( ye hai aam jindagi):

```bash
# do ssh 
ssh <user>@<app_server>

# run the command
chmod 755 /tmp/xfusioncorp.sh
```

### Mentos Life solution steps:
**very simple**: Don't SSH just execute it remotely without an interactive session (ye hai mentos jindagi):

```bash
ssh user@app-server-1 "chmod 755 /tmp/xfusioncorp.sh"
```
you can choose either of the above methods to grant the necessary permissions to the script.

---

## Permission Breakdown

### Numeric Mode: `755`

| Permission Class | Value | Permissions |
|------------------|----|-------------|
| Owner            | 7  | Read, Write, Execute (rwx) |
| Group            | 5  | Read, Execute (r-x) |
| Others           | 5  | Read, Execute (r-x) |

### Calculation
- Number system
```text
7 = 4 + 2 + 1 = Read + Write + Execute
5 = 4 + 0 + 1 = Read + Execute
5 = 4 + 0 + 1 = Read + Execute
```

- Alphabetic system
```text
r - read (4)
w - write (2)
x - execute (1)
```
So `755` translates to: `rwxr-xr-x`

---

## Verification

Check the file permissions using:

```bash
ls -l /tmp/xfusioncorp.sh
```

Expected output:

```text
-rwxr-xr-x ... ...
```

---

## Why Use 755?

- Allows the script to be executed by all users.
- Restricts modification privileges to the file owner.
- Follows standard Linux security practices for executable scripts.

---

## Summary

| Action | Command |
|----------|----------|
| Grant execute permissions to all users | `chmod 755 /tmp/xfusioncorp.sh` |
| Verify permissions | `ls -l /tmp/xfusioncorp.sh` |

---

## Key Takeaway

Using:

```bash
chmod 755 /tmp/xfusioncorp.sh
```

ensures that:

✅ The script is executable by everyone.  
✅ Only the owner can modify the script.  
✅ Appropriate security and accessibility are maintained.