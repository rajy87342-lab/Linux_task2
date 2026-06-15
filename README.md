# ðŸ§ Linux Task 02 Report â€” Users, Groups & File Permissions

**Date:** June 15, 2026
**Intern:** [AKASH yadav]

---

## ðŸŽ¯ Task Objectives

- Understand Linux user management and group structure
- Create users and groups, assign memberships
- Manage file ownership using `chown`
- Modify file permissions using `chmod`
- Analyse permission values and their real-world use cases
- Apply the Principle of Least Privilege

---

## âœ… Part A: Understanding Users

### ðŸ’» Commands Run

```bash
$ whoami
root

$ id
uid=0(root) gid=0(root) groups=0(root)

$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
student1:x:1001:1004::/home/student1:/bin/sh
student2:x:1002:1005::/home/student2:/bin/sh
student3:x:1003:1006::/home/student3:/bin/sh
```

### ðŸ“‹ Answers

**â“ What is your current username?**
`root` â€” the superuser with full system access.

**â“ What is UID?**
UID (User ID) is a unique number assigned to every user on a Linux system. The root user always has UID = 0. Regular users typically start from UID 1000+.

**â“ What is GID?**
GID (Group ID) is a unique number assigned to a group. Every user has a primary GID. Users can also belong to additional secondary groups. Root's GID is 0.

**â“ What information does /etc/passwd contain?**
Each line in `/etc/passwd` represents one user and contains 7 fields separated by colons:

```
username : password : UID : GID : info : home_directory : shell
root     :    x     :  0  :  0  : root :     /root      : /bin/bash
```

- `x` in the password field means the actual password hash is stored in `/etc/shadow` (for security)
- Home directory is where the user lands on login
- Shell is the command interpreter assigned to that user

### ðŸ“¸ Screenshots

![whoami and id](screenshots/01_whoami_id.png)

![/etc/passwd](screenshots/02_etc_passwd.png)

---

## âœ… Part B: Create Users & Groups

### ðŸ’» Commands Run

```bash
# Create groups
$ groupadd interns
$ groupadd cyberteam

# Create users with home directories
$ useradd -m student1
$ useradd -m student2
$ useradd -m student3

# Add users to groups
$ usermod -aG interns student1
$ usermod -aG interns student2
$ usermod -aG cyberteam student2
$ usermod -aG cyberteam student3
```

### ðŸ–¥ï¸ Group Membership Summary

| User     | UID  | Primary Group | Secondary Groups       |
|----------|------|---------------|------------------------|
| student1 | 1001 | student1      | interns                |
| student2 | 1002 | student2      | interns, cyberteam     |
| student3 | 1003 | student3      | cyberteam              |

### ðŸ’» Verification

```bash
$ id student1
uid=1001(student1) gid=1004(student1) groups=1004(student1),1002(interns)

$ id student2
uid=1002(student2) gid=1005(student2) groups=1005(student2),1002(interns),1003(cyberteam)

$ id student3
uid=1003(student3) gid=1006(student3) groups=1006(student3),1003(cyberteam)

$ cat /etc/group | grep -E 'interns|cyberteam'
interns:x:1002:student1,student2
cyberteam:x:1003:student2,student3
```

### ðŸ“¸ Screenshots

![Create Groups and Users](screenshots/03_groups_users.png)

![Verify Group Membership](screenshots/04_id_verify.png)

---

## âœ… Part C: File Ownership

### ðŸ’» Commands Run

```bash
# Create folder and files
$ mkdir CyberSecurity_Project
$ touch CyberSecurity_Project/report.txt
$ touch CyberSecurity_Project/notes.txt
$ touch CyberSecurity_Project/credentials.txt

# Check original ownership
$ ls -l CyberSecurity_Project/
total 0
-rw-r--r-- 1 root root 0 Jun 15 05:02 credentials.txt
-rw-r--r-- 1 root root 0 Jun 15 05:02 notes.txt
-rw-r--r-- 1 root root 0 Jun 15 05:02 report.txt

# Change ownership of report.txt
$ chown student1:interns CyberSecurity_Project/report.txt

# Verify after chown
$ ls -l CyberSecurity_Project/
total 0
-rw-r--r-- 1 root     root    0 Jun 15 05:02 credentials.txt
-rw-r--r-- 1 root     root    0 Jun 15 05:02 notes.txt
-rw-r--r-- 1 student1 interns 0 Jun 15 05:02 report.txt
```

### ðŸ“‹ Ownership Change Record

| Field          | Value                                    |
|----------------|------------------------------------------|
| File           | `report.txt`                             |
| Original Owner | `root:root`                              |
| New Owner      | `student1:interns`                       |
| Command Used   | `chown student1:interns report.txt`      |

### ðŸ“¸ Screenshots

![Ownership Before chown](screenshots/05_ownership_before.png)

![Ownership After chown](screenshots/06_chown.png)

---

## âœ… Part D: File Permissions

### ðŸ’» Commands Run

```bash
$ touch security_policy.txt

# Default permissions
$ ls -l security_policy.txt
-rw-r--r-- 1 root root 0 Jun 15 05:02 security_policy.txt

# Stage 1: Read Only (r--r--r--)
$ chmod 444 security_policy.txt
-r--r--r-- 1 root root 0 Jun 15 05:02 security_policy.txt

# Stage 2: Read & Write (rw-rw-r--)
$ chmod 664 security_policy.txt
-rw-rw-r-- 1 root root 0 Jun 15 05:02 security_policy.txt

# Stage 3: Full Access (rwxrwxrwx)
$ chmod 777 security_policy.txt
-rwxrwxrwx 1 root root 0 Jun 15 05:02 security_policy.txt
```

### ðŸ“‹ Permission Stages

| Stage       | Command      | Symbolic     | Meaning                          |
|-------------|--------------|--------------|----------------------------------|
| Default     | â€”            | `rw-r--r--`  | Owner:rw, Group:r, Others:r      |
| Read Only   | `chmod 444`  | `r--r--r--`  | Everyone can only read           |
| Read & Write| `chmod 664`  | `rw-rw-r--`  | Owner+Group:rw, Others:r         |
| Full Access | `chmod 777`  | `rwxrwxrwx`  | Everyone has full access âš ï¸      |

### ðŸ“¸ Screenshot

![chmod Stages](screenshots/07_chmod_stages.png)

---

## âœ… Part E: Permission Analysis

### How Linux Permissions Work

```
  - r w x r - x r - -
  â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â””â”€ Others: execute
  â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â””â”€â”€â”€ Others: write
  â”‚ â”‚ â”‚ â”‚ â”‚ â”‚ â””â”€â”€â”€â”€â”€ Others: read
  â”‚ â”‚ â”‚ â”‚ â”‚ â””â”€â”€â”€â”€â”€â”€â”€ Group: execute
  â”‚ â”‚ â”‚ â”‚ â””â”€â”€â”€â”€â”€â”€â”€â”€â”€ Group: write
  â”‚ â”‚ â”‚ â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Group: read
  â”‚ â”‚ â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Owner: execute
  â”‚ â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Owner: write
  â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€ Owner: read
  (file type: - = file, d = directory)

  r = 4,  w = 2,  x = 1
```

### ðŸ“‹ Permission Values Explained

| Permission | Symbolic     | Owner | Group | Others | Real-world Use Case                         |
|------------|--------------|-------|-------|--------|---------------------------------------------|
| **755**    | `rwxr-xr-x`  | rwx   | r-x   | r-x    | Public directories, web server scripts      |
| **644**    | `rw-r--r--`  | rw-   | r--   | r--    | Config files, web pages, text documents     |
| **777**    | `rwxrwxrwx`  | rwx   | rwx   | rwx    | âš ï¸ Almost never â€” major security risk      |
| **600**    | `rw-------`  | rw-   | ---   | ---    | SSH private keys, password files            |
| **700**    | `rwx------`  | rwx   | ---   | ---    | Private scripts, personal executable files  |

### ðŸ“¸ Screenshot

![Permission Analysis](screenshots/08_permission_analysis.png)

---

## âœ… Part F: Security Challenge

> **Scenario:** You are a Linux Administrator. Assign the correct permissions to each file.

### ðŸ“‹ Recommended Permissions Table

| File                  | Recommended Permission | Reason                                                                        |
|-----------------------|------------------------|-------------------------------------------------------------------------------|
| `password_backup.txt` | **600** (`rw-------`)  | Extremely sensitive. Only the owner should ever read or write it. No group or others access under any circumstance. |
| `public_notice.txt`   | **644** (`rw-r--r--`)  | Public information. Admin writes it, but everyone (staff, users) needs to read it. |
| `system_log.txt`      | **640** (`rw-r-----`)  | Sysadmin writes, the admin group can read for monitoring. No access for others. |
| `personal_notes.txt`  | **600** (`rw-------`)  | Strictly personal. Owner only. No other user should see personal data.        |

### ðŸ’» Commands

```bash
$ chmod 600 password_backup.txt
$ chmod 644 public_notice.txt
$ chmod 640 system_log.txt
$ chmod 600 personal_notes.txt
```

### ðŸ“¸ Screenshot

![Security Challenge](screenshots/09_security_challenge.png)

---

## âœ… Part G: Linux Security Research

### â“ Why are file permissions important?

File permissions are the first line of defence in Linux security. They control who can read, modify, or execute every file and directory on the system. Without proper permissions, any user or malicious process could access, alter, or delete critical system files, steal sensitive data like passwords or keys, or inject malicious code into executables. Permissions enforce boundaries between users and protect the system's integrity.

---

### â“ What happens if sensitive files are given 777 permissions?

Giving a sensitive file `777` permissions means every single user on the system â€” and any process running under any user â€” can read, write, and execute it. For a file like `/etc/shadow` (password hashes) or an SSH private key, this would be catastrophic:

- Any user can read and steal the data
- Any user can overwrite or corrupt the file
- A malicious script or malware running as any user gains access instantly
- This is one of the most common misconfigurations exploited in real-world attacks

---

### â“ What is the Principle of Least Privilege?

The Principle of Least Privilege (PoLP) states that every user, process, or system component should be granted **only the minimum level of access required** to perform its specific function â€” and nothing more.

For example:
- A web server process only needs read access to web files, not write or execute on system files
- A regular intern should not have root/sudo access
- A log file should be readable by the admin group, not writable by others

This limits the damage an attacker can do if they compromise any single account.

---

### â“ Why do organizations restrict user access?

Organizations restrict access for several critical reasons:

- **Prevent insider threats** â€” employees with excessive access can intentionally or accidentally cause damage
- **Limit breach impact** â€” if one account is compromised, restricted permissions stop the attacker from moving laterally
- **Regulatory compliance** â€” standards like ISO 27001, PCI-DSS, HIPAA, and SOC 2 mandate access control
- **Data confidentiality** â€” personal data, financial records, and trade secrets must only be accessible to authorised personnel
- **Audit and accountability** â€” restricted access makes it easier to trace who accessed or changed what

### ðŸ“¸ Screenshot

![Linux Security Research](screenshots/10_security_research.png)

---

## ðŸ“ Repository Structure

```
Linux_Task_02_YourName/
â”‚
â”œâ”€â”€ README.md                        â† This file
â””â”€â”€ screenshots/
    â”œâ”€â”€ 01_whoami_id.png
    â”œâ”€â”€ 02_etc_passwd.png
    â”œâ”€â”€ 03_groups_users.png
    â”œâ”€â”€ 04_id_verify.png
    â”œâ”€â”€ 05_ownership_before.png
    â”œâ”€â”€ 06_chown.png
    â”œâ”€â”€ 07_chmod_stages.png
    â”œâ”€â”€ 08_permission_analysis.png
    â”œâ”€â”€ 09_security_challenge.png
    â””â”€â”€ 10_security_research.png
```

---

*Submitted via GitHub | White Band Associates â€” Linux Task 02*
