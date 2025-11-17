# 🔍 Incident Investigation: SOC168 – Whoami Command Detected in Request Body

## 📌 Alert Summary
| Field | Details |
|------|---------|
| **Alert Name** | SOC168 – Whoami Command Detected in Request Body |
| **Event ID** | 118 |
| **Type** | Web Attack |
| **Severity** | High |
| **Alert Trigger** | Request Body contained the string `"whoami"` |
| **Device Action** | Allowed |
| **Event Time** | Feb 28, 2022 – 04:12 AM |
| **Source IP** | 61.177.172.87 (External – Internet) |
| **Destination IP** | 172.16.17.16 (Internal Web Server) |
| **URI** | https://172.16.17.16/video/ |
| **Request Method** | POST |

---

## 🎯 Attack Description

This alert indicates a **Command Injection attempt** where the attacker tried to execute the `whoami` command on the server through a POST request.  
Command Injection is a high-severity web attack that targets vulnerable input fields to execute OS commands on the backend system.

**Purpose of `whoami`**
- Used by attackers to verify if they gained remote execution
- Helps identify current machine privileges (root/admin compromise value)

---

## 🕵️ Investigation Steps

### **1️⃣ IP Reputation Check**
Security tools checked:
- VirusTotal  
- Results: **Clean**, but belongs to external network — suspicious as it’s not business-related traffic.

✔ Traffic came **from Internet → Company Network**

---

### **2️⃣ Endpoint Forensics**

🖥 **Target Host**  
- Hostname: `WebServer1004`
- OS: Ubuntu 20.04
- Primary User: `webadmin3`
- Last Login: Feb 08, 2022

🧾 **Terminal Activity**
| Time | Command Executed | User |
|------|------------------|-----|
| 04:11 | `ls` | root |
| 04:12 | `whoami` | root |
| 04:13 | `uname` | root |

🚨 This is critical — commands were successfully executed as **root**.  
➡ This confirms **Command Injection was successful**.

---

### **3️⃣ Network Activity Check**
All outbound traffic from the server investigated:
| Destination IP | Reputation | Result |
|----------------|-----------|--------|
| 54.242.165.36 | AWS | Clean |
| 198.54.117.250 | NameCheap | Clean |

➡ Communication appears benign but still abnormal for a web server.

---

## 🧠 Conclusion of Investigation

| Criteria | Result |
|---------|-------|
| Malicious? | ✔ Yes |
| Attack Type | ✔ Command Injection |
| Direction of Traffic | ✔ Internet → Company Network |
| Successful Exploitation | ✔ Yes — root command execution |
| Escalation Needed | ✔ Yes (High-Severity Web Compromise) |

---

## 📝 Analyst Notes

An attacker from an external IP attempted a web-based command injection attack using the `whoami` command in the POST request body. Endpoint logs confirmed execution of multiple OS commands (`ls`, `whoami`, `uname`) under the **root** user — indicating full system compromise.

**This incident required Tier-2 escalation** due to unauthorized root-level system control.

---

## 🛡 Recommended Actions

| Action | Priority |
|-------|----------|
| Patch vulnerable website input validation | 🔥 High |
| Block attacker IP at the perimeter firewall | 🔥 High |
| Reset root/user credentials on server | 🔥 High |
| Deploy/verify WAF rules against OS command patterns | Medium |
| Conduct full malware scan on server | Medium |

---

## 🗂 Artifacts / IOCs

| IOC Type | Value | Comment |
|---------|-------|---------|
| Source IP | 61.177.172.87 | Attacker |
| Observed Commands | `ls`, `whoami`, `uname` | Executed as root |
| Affected Host | 172.16.17.16 | WebServer1004 |

---

## 🏁 Summary

This alert revealed a **successful remote code execution** attack via command injection on a production Linux Web Server. The attacker gained **root-level access**, posing a severe threat to business infrastructure.

This case demonstrates skills in:
- Web attack detection
- Threat triage and log analysis
- Endpoint investigation
- Incident classification and escalation
- SOC playbook execution

---

📌 *This investigation is part of my Blue Team training on LetsDefend SOC platform.*
