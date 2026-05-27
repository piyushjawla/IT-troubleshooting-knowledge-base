# Linux Firewall troubleshooting using UFW (ssh blocked)

## Objective

The purpose of this lab was to understand how linux firewalls control network traffic and troubleshoot SSH connectivity issues caused by firewall rules using UFW.

This lab helped reinforce:

- firewall fundamentals
- SSH troubleshooting
- network filtering concepts
- Linux security controls
- layered troubleshooting methodology

---

## Environment

- Guest OS: Ubuntu
- Remote access:
  - SSH
- Firewall:
  - UFW (Uncomplicated Firewall)
- Host OS: Windows

---

## Understanding Firewalls

A firewall controls which network traffic is permitted or denied.

Rather than simply "blocking the internet", firewalls act as traffic filters that inspect and control communication based on configured rules.

Typic firewall decisions include:

| Rule | Meaning |
| --- | --- |
| Allow SSH | permit TCP port 22 |
| Block HTTP | Deny TCP port 80 |
| Allow Outbound | permit outgoing traffic |

Linux firewalls commonly operate using:

- default policies
- explicit allow/deny rules

---

# Understanding UFW

Ubuntu commonly uses:

```bash
ufw
```

which stands for:

```text
Uncomplicated Firewall
```

UFW functions as a simplified interface for linux firewall systems built on:

- netfilter
- iptables

This allows administrators to manage firewall behaviour more easily.

---

## Verifying SSH service availability

Before changing firewall settings, the SSH service was verified.

```bash
sudo systemctl status ssh
```

The service showed:

```text
active(running)
```  
<img width="732" height="317" alt="ssh-active-before-firewall" src="https://github.com/user-attachments/assets/8f7c726e-c454-411a-a075-7ee18ac71c88" />  

### Why this step matters

Effective troubleshooting begins by establishing a known-good baseline.

This confirmed:

- SSH installed
- SSH listening
- service functioning

before introducing firewall changes.

---

## Verifying working SSH connectivity

SSH access from Windows host was tested:

```cmd
ssh admin@192.168.1.4
```

Connection succeeded.  
<img width="645" height="526" alt="ssh-working-before-ufw" src="https://github.com/user-attachments/assets/5f939018-0ddc-4b0b-acaf-a944a1d88710" />  

This confirmed:

- network connectivity working
- SSH functioning
- remote access available

---

## Inspecting firewall status

The firewall status was checked:

```bash
sudo ufw status
```

Initially, UFW showed:

```text
inactive
```  
<img width="297" height="37" alt="ufw-inactive" src="https://github.com/user-attachments/assets/59d3f440-a858-441a-bf10-90cf208201e2" />  

### Understandig inactive firewall status

An inactive firewall means:

- firewall engine disabled
- traffic not actively filtered
- communication allowed without firewall enforcement

---

## Enabling UFW

The firewall was enabled:

```bash
sudo ufw enable
```

<img width="702" height="227" alt="ufw-enabled" src="https://github.com/user-attachments/assets/8458e295-e6e1-4092-b89a-50f377f434f0" />  

### Understanding default firewall policy

Firewall policy was reviewed:

```bash
sudo ufw status verbose
```

The output showed:

```text
Default: deny (incoming), allow (outgoing)
```

<img width="502" height="80" alt="ufw-verbose-default-policy" src="https://github.com/user-attachments/assets/d68ef2d7-549e-4ccd-a784-754ef10b0422" />  

This means:

| Traffic type | Policy |
| --- | --- |
| Incoming | Denied unless allowed |
| Outgoing | Allowed |

This is a common:

> defauly deny

security model.

---

## Reviewing firewall rules

Firewall rules were inspected:

```bash
sudo ufw status numbered
```

No explicit rules appeared.

This indicated:

- firewall active
- no user-defined allow rules configured

### Important firewall concept

No rules displayed does **not** mean:

```text
firewall inactive
```

It only means:

- default policy applies
- no custom exceptions exist

---

## Testing SSH after firewall activation

SSH was tested again from the Windows host:

```cmd
ssh admin@192.168.1.4
```

Connection failed.

The client eventually showed:

```text
Connection timed out
```

<img width="781" height="60" alt="ssh-failed-after-ufw" src="https://github.com/user-attachments/assets/6e49dbb8-8445-4508-954f-1edb32148bbd" />  

---

## Understanding timeout vs refused

This lab created an important troubleshooting distinction.

Earlier SSH troubleshooting produced:

```text
Connection refused
```

which indicated:

- service unavailable
- host reachable
- port not accepting connections

This firewall scenario produced:

```text
Connection timed out
```

which indicated:

- traffic filtered or dropped
- packets blocker before reaching SSH service

This distinction is critical in network troubleshooting.

---

## Investigating firewall configuration

Firewall details were reviewed:

```bash
sudo ufw status verbose
```

<img width="502" height="80" alt="ufw-verbose-default-policy" src="https://github.com/user-attachments/assets/30cadf89-c7d6-48ff-9527-d3ebabfb2327" />  

This confirmed:

- firewall active
- incoming traffic denied
- SSH not explicitly allowed

---

## Allowing SSH through firewall

SSH was restored using:

```bash
sudo ufw allow ssh
```

<img width="312" height="47" alt="ufw-allow-ssh" src="https://github.com/user-attachments/assets/f2aefdbb-c67b-4a39-b5c7-5a406427e095" />  

### Understanding UFW profiles

UFW translates:

```text
ssh
```

into:

```text
TCP port 22
```

and creates an allow rule.

---

## Verifying firewall rule

Rules were reviewed again:

```bash
sudo ufw status numbered
```

The output now showed an SSH rule.  

<img width="490" height="121" alt="ufw-ssh-rule-present" src="https://github.com/user-attachments/assets/0ccb3221-682c-4bca-9b5b-a5cc0375742e" />  

This confirmed: 

- SSH traffic permitted
- firewall exception active

---

## Final validation

SSH connectivity was tested again:

```cmd
ssh admin@192.168.1.4
```

Connection succeeded.

<img width="710" height="596" alt="ssh-restored-firewall" src="https://github.com/user-attachments/assets/34f317cc-b325-4eeb-a892-b6e778c0bc75" />  

This confirmed that:

- firewall rule restored access
- SSH service remained healthy
- firewall filtering caused the original issue

---

# Key concepts learned

| Concept | Explanation |
| --- | --- |
| Firewall | Network traffic filter |
| UFW | Ubuntu firewall manager |
| Default deny | Block unless explicitly allowed |
| Port 22 | SSH communication port |
| Timeout | Traffic filtered or dropped |
| Refused | Service unavailable |

---

# Real world relevance

Firewall related issues are common in:

- Linux servers
- Cloud infrastructure
- SSH administration
- remote system management
- hardened systems

Typical symptoms include:

- inaccessible SSH
- remote administration failure
- blocked ports
- unexpected connectivity loss

---

# Lessons learned

- Services and firewalls operate at different troubleshooting layers
- Running services may still be unreachable through firewall filtering
- UFW enforces traffic rules using default policies and explicit exceptions
- `ufw status verbose` provides valuable firewall diagnostics
- `ufw allow ssh` permits SSH traffic through TCP port 22
- Connection timeout and connection refused indicate different underlying problems
- Troubleshooting requires isolating network, firewall, and service layers independently










