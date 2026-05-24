# Penetration Testing Report – DC-4

## Target Information

| Field | Details |
|---|---|
| Machine Description | A deliberately vulnerable lab machine for practicing web application exploits, privilege escalation, and post-exploitation techniques. |
| Target IP | `192.168.0.152` |
| Vulnerability Name | Weak Passwords & Privilege Escalation (Bruteforce Login + Insecure Script) |
| Port / Service | `TCP 80 / HTTP` |
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
nmap -p- -sC -sV 192.168.0.152 --open
```

![Step 2](images/step2.png)

---

## Step 3 – Discover Login Page

A web login page was identified on the target application.

![Step 3](images/step3.png)

---

## Step 4 – Capture Request Using Burp Suite

The authentication request was intercepted using Burp Suite.

![Step 4](images/step4.png)

---

## Step 5 – Send Request to Intruder

The captured request was forwarded to Burp Suite Intruder.

![Step 5](images/step5.png)

---

## Step 6 – Configure Password Bruteforce

The password parameter was selected for bruteforce testing using the `rockyou.txt` wordlist.

![Step 6](images/step6.png)

---

## Step 7 – Load Wordlist

The `rockyou.txt` wordlist was loaded into Burp Intruder.

![Step 7](images/step7.png)

---

## Step 8 – Prepare Attack

Burp Suite loaded all password payloads for testing.

![Step 8](images/step8.png)

---

## Step 9 – Start Bruteforce Attack

The attack was initiated from Burp Suite Intruder.

![Step 9](images/step9.png)

---

## Step 10 – Identify Valid Credentials

A different response size indicated a successful login attempt.

Password discovered:

```text
happy
```

![Step 10](images/step10.png)

---

## Step 11 – Login Successful

Authenticated access to the application was achieved.

![Step 11](images/step11.png)

---

## Step 12 – Execute System Commands

The application allowed execution of system-level commands.

![Step 12](images/step12.png)

---

## Step 13 – Gain Reverse Shell

A reverse shell connection was established using Burp Suite and Netcat.

```bash
nc -lvnp 4444
```

![Step 13](images/step13.png)

---

## Step 14 – Discover Old Passwords

Old passwords were identified inside a local file.

![Step 14](images/step14.png)

---

## Step 15 – Brute Force SSH Using Hydra

Hydra was used with discovered passwords to identify valid credentials.

```bash
hydra -L users.txt -P passwords.txt ssh://192.168.0.152
```

![Step 15](images/step15.png)

---

## Step 16 – Discover Credentials for Charles

Valid credentials for user `charles` were identified.

```text
^xHhA&hvim0y
```

![Step 16](images/step16.png)

---

## Step 17 – Discover Vulnerable Script

A misconfigured script with elevated privileges was identified.

![Step 17](images/step17.png)

---

## Step 18 – Abuse Script to Modify `/etc/passwd`

The vulnerable script was abused to append entries into `/etc/passwd`.

![Step 18](images/step18.png)

---

## Step 19 – Escalate Privileges

The modified configuration enabled privilege escalation.

![Step 19](images/step19.png)

---

## Step 20 – Obtain Root Access

Root-level access was successfully obtained.

![Step 20](images/step20.png)

#Proof

![Step 21](images/step21.png)

---

# Remediation

- Use strong passwords and enforce password complexity policies.
- Enable account lockout and rate limiting.
- Remove old or plaintext passwords from the system.
- Restrict access to administrative functionality.
- Audit and secure privileged scripts.
- Apply least-privilege principles.
- Keep the operating system and applications updated.

---

# Impact

- Unauthorized login using weak credentials.
- Remote command execution on the server.
- Exposure of user credentials.
- Lateral movement between user accounts.
- Privilege escalation to root access.
- Full compromise of the target machine.

---

# References

- https://owasp.org/www-community/attacks/Weak_passwords
- https://owasp.org/www-community/Broken_Access_Control
- https://www.ncsc.gov.uk/collection/passwords
