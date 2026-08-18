# Hack The Box Academy: Footprinting Module

## Overview

I completed the Footprinting module in the Hack The Box Academy CPTS path. The module focused on manual enumeration, service interaction, and the process of correlating information across multiple systems and protocols.

This repository entry is intentionally sanitized. It does **not** contain target addresses, flags, passwords, private keys, challenge answers, or exact lab solutions.

> All activity described here was performed in authorized Hack The Box Academy lab environments.

---

## Topics Covered

- Enumeration principles and methodology
- Infrastructure-based enumeration
- Domain and DNS information gathering
- Cloud-resource discovery concepts
- FTP
- SMB
- NFS
- DNS
- SMTP
- IMAP and POP3
- SNMP
- MySQL
- MSSQL
- Oracle TNS
- IPMI
- Linux remote-management protocols
- Windows remote-management protocols

---

## Core Methodology

The module reinforced a repeatable enumeration process:

1. Identify all exposed TCP and UDP services.
2. Collect banners, versions, hostnames, certificates, and capabilities.
3. Enumerate each service manually before considering automated exploitation.
4. Record users, internal names, paths, shares, scripts, and authentication mechanisms.
5. Test discovered credentials only against relevant in-scope services.
6. Correlate information from separate services.
7. Re-enumerate after gaining authenticated or local access.
8. Document both successful paths and failed assumptions.

The most useful questions were:

- What can I see?
- Why is it exposed?
- What information does it disclose?
- What trust relationship does it reveal?
- How can this finding inform enumeration of another service?
- What becomes visible only after authentication or local access?

---

## Lab Progression

### Easy Lab

The first lab began with a service emphasized in the scenario, but a full port scan revealed additional services that formed the actual access path.

Sanitized chain:

```text
Network enumeration
-> DNS information disclosure
-> Nonstandard file-transfer service
-> Exposed authentication material
-> Remote shell access
-> Proof file recovered
```

Key lessons:

- Do not restrict enumeration to the service highlighted by the client.
- Always check the full TCP port range.
- Nonstandard ports may expose duplicate or separately configured services.
- Hidden directories can contain high-value authentication material.
- Private SSH keys must be protected like passwords.
- A home-directory search and a filesystem-wide search have very different scope.

### Medium Lab

The second lab demonstrated how an internal file-sharing weakness could lead to interactive Windows access and eventually a locally available database.

Sanitized chain:

```text
Network enumeration
-> Misconfigured internal file share
-> Sensitive support record
-> Credential reuse
-> Interactive Windows access
-> Additional plaintext credential
-> Administrative access
-> Local database enumeration
-> Target account recovered
```

Key lessons:

- NFS can appear on Windows systems in mixed environments.
- Support tickets and shared folders frequently contain configuration files and credentials.
- Application passwords are often reused for operating-system accounts.
- A database may listen only locally and remain invisible during external scanning.
- Low-privileged GUI access can reveal files, tools, and locally bound services.
- Reused administrative passwords can turn a limited foothold into full system access.

### Hard Lab

The third lab required correlation across TCP, UDP, management, mail, SSH, and database services.

Sanitized chain:

```text
TCP and UDP enumeration
-> Weak management-service configuration
-> Command-argument information disclosure
-> Mailbox access
-> Exposed SSH authentication material
-> Local shell access
-> Shell-history analysis
-> Local database discovery
-> Target account recovered
```

Key lessons:

- TCP-only enumeration is incomplete; management protocols often use UDP.
- A service supporting a modern protocol version may still expose an older, weaker version.
- SNMP can reveal processes, scripts, command arguments, and sensitive operational data.
- Contextual words in the scenario can help prioritize hypotheses, but they must be validated.
- Mailboxes may contain keys, reset instructions, internal discussions, and credentials.
- Shell-history and client-history files can reveal locally available services and exact database structures.
- Locally bound services become visible only after obtaining host access.
- Complex attack paths are often chains of information disclosures rather than one dramatic exploit.

---

## Representative Commands

The following generic commands represent techniques practiced during the module. Placeholders are used intentionally.

### Full TCP enumeration

```bash
nmap -sV -sC -p- TARGET
```

### Targeted UDP enumeration

```bash
sudo nmap -sU -sV -sC -p PORTS TARGET
```

### DNS record and zone-transfer testing

```bash
dig NS DOMAIN @DNS_SERVER
dig SOA DOMAIN @DNS_SERVER
dig AXFR DOMAIN @DNS_SERVER
```

### NFS enumeration

```bash
showmount -e TARGET
sudo nmap --script 'nfs*' -sV -p111,2049 TARGET
sudo mount -t nfs TARGET:/EXPORTED_PATH LOCAL_MOUNT -o nolock
```

### Secure IMAP interaction

```bash
openssl s_client -connect TARGET:993 -quiet
```

Example IMAP commands:

```text
a LOGIN USER PASSWORD
a LIST "" "*"
a STATUS FOLDER (MESSAGES UNSEEN)
a SELECT FOLDER
a SEARCH ALL
a FETCH 1:* BODY[]
```

### SNMP enumeration

```bash
snmpwalk -v2c -c COMMUNITY TARGET
```

### SSH private-key use

```bash
chmod 600 PRIVATE_KEY
ssh-keygen -y -f PRIVATE_KEY >/dev/null
ssh -i PRIVATE_KEY USER@TARGET
```

### Local MySQL enumeration

```bash
mysql -u USER -p
```

```sql
SHOW DATABASES;
USE DATABASE_NAME;
SHOW TABLES;
DESCRIBE TABLE_NAME;
SELECT * FROM TABLE_NAME;
```

---

## Common Mistakes and Corrections

| Mistake | Correction |
|---|---|
| Scanning only the service mentioned in the scenario | Enumerate the complete attack surface |
| Running only a default TCP scan | Scan all TCP ports and relevant UDP ports |
| Treating a hostname as a transferable DNS zone | AXFR applies to configured zones, not arbitrary host records |
| Mounting the NFS root instead of the exported path | Mount the exact export reported by `showmount` or Nmap |
| Assuming local user permissions map cleanly to NFS | Account for UID/GID and anonymous identity mapping |
| Mixing SNMPv3 syntax with SNMPv2c community strings | Select options appropriate to the SNMP version |
| Trusting a mailbox folder name | Check actual message counts before fetching |
| Removing SSH key boundary lines | Preserve the complete OpenSSH/PEM structure |
| Assuming a service is absent because it is not remotely exposed | Re-enumerate localhost after gaining host access |
| Manually transcribing ambiguous credentials | Copy exact values when possible and verify character case |

---

## Security Findings Demonstrated

- Unauthorized DNS information disclosure
- Excessive service-banner disclosure
- Public or overly broad file-share access
- Plaintext credentials stored in support records
- Credential reuse across applications and operating systems
- Exposed SSH private keys
- Weak SNMP community strings
- Sensitive command-line arguments exposed through management interfaces
- Sensitive information retained in shell and client history
- Locally accessible databases containing recoverable account data
- Administrative password reuse

---

## Defensive Recommendations

- Restrict DNS zone transfers to authorized secondary name servers.
- Remove unnecessary service banners and internal hostnames from externally visible responses.
- Limit NFS and SMB shares by network, identity, and least privilege.
- Prohibit credentials and private keys in tickets, email, and shared folders.
- Enforce unique passwords through technical controls and password-manager adoption.
- Disable legacy SNMP versions and require SNMPv3 authentication and encryption.
- Prevent secrets from appearing in process arguments and management telemetry.
- Bind administrative databases appropriately and restrict local access.
- Protect or disable shell-history files on sensitive administrative systems.
- Rotate any credential or key exposed through logs, mail, tickets, or management services.
- Monitor for unusual access to file shares, mailboxes, management protocols, and local databases.

---

## Final Takeaway

The most important lesson from this module was that enumeration is not a checklist of isolated tools. It is the process of building context.

A hostname discovered through DNS can identify another service. A support ticket can expose a credential. That credential can unlock a mailbox or desktop. A mailbox can disclose a key. A shell can reveal a locally bound database. Individually, each finding may look limited; together, they can create a complete path through an environment.

The quality of an assessment depends not only on what is discovered, but on recognizing how each discovery changes the next question.

---

## Skills Practiced

`Nmap` · `dig` · `FTP` · `SMB/RPC` · `NFS` · `OpenSSL` · `IMAP` · `POP3` · `SNMP` · `SSH` · `RDP` · `MySQL` · `MSSQL` · Linux enumeration · Windows enumeration · credential analysis · attack-path correlation

