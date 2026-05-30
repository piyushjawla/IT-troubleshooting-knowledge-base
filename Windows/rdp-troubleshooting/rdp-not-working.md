# Windows RDP Troubleshooting – Remote Desktop Not Working

## Objective

The purpose of this lab was to understand how Windows Remote Desktop Protocol (RDP) functions and troubleshoot remote access failures caused by network topology and RDP configuration.

This lab helped reinforce:

- Windows remote administration
- RDP fundamentals
- TCP listener concepts
- VirtualBox networking modes
- layered troubleshooting
- remote connectivity diagnostics

---

# Environment

Host System:

- Windows 11 Home Single Language

Target System:

- Windows 10 Pro VM
- VirtualBox

Remote Access:

- Remote Desktop Protocol (RDP)

---

# Understanding RDP

RDP stands for:

```text
Remote Desktop Protocol
```

Microsoft's remote access technology used to:

- remotely log into systems
- provide GUI desktop access
- administer Windows machines
- support users remotely

Unlike SSH:

| SSH | RDP |
|---|---|
| Command line | Graphical desktop |
| Common in Linux | Common in Windows |
| Terminal access | Full desktop session |

RDP commonly uses:

```text
TCP 3389
```

---

# Understanding RDP Host Capability

Not all Windows editions support hosting Remote Desktop.

| Windows Edition | RDP Host Support |
|---|---|
| Home | No |
| Pro | Yes |
| Enterprise | Yes |

The Windows 10 VM was verified as:

```text
Windows 10 Pro
```

using:

```cmd
winver
```

<img width="472" height="462" alt="win10pro-confirm" src="https://github.com/user-attachments/assets/36ea810e-9203-4670-8e31-aa6bcfc8dab9" />  


This confirmed RDP hosting capability.

---

# Beginning Troubleshooting

Rather than assuming RDP failure immediately, basic connectivity was tested first.

This follows layered troubleshooting methodology.

---

# Initial Connectivity Failure

The Windows 10 VM originally used:

- NAT
- Internal Network

configuration.

The VM received:

```text
10.x.x.x
```

addressing.

When tested from the host:

```cmd
ping VM_IP
```

<img width="726" height="252" alt="ping-vm-failed" src="https://github.com/user-attachments/assets/53660353-811a-422e-ab00-8719e20d6d2a" />  


the request timed out.

This indicated:

- remote access testing should stop
- basic reachability required investigation first

---

# Understanding NAT Behavior

VirtualBox NAT behaves similarly to a router.

Typical behavior:

| Direction | Expected |
|---|---|
| VM → Internet | Works |
| VM → Host | Often works |
| Host → VM | Often blocked |

The VM successfully reached:

```cmd
ping 8.8.8.8
```

and:

```cmd
ping HOST_IP
```

while host-to-VM communication failed.

This demonstrated:

- NAT isolation
- asymmetric connectivity
- network topology affecting reachability

---

# Moving to Bridged Networking

To support realistic remote administration, networking was changed to:

```text
Bridged Adapter
```

while disabling:

```text
Internal Network
```

Bridged mode places the VM directly on the LAN.

The VM then received:

```text
192.168.1.x
```

addressing.

Host testing:

```cmd
ping VM_IP
```

now succeeded.

<img width="712" height="292" alt="ping-host-vm" src="https://github.com/user-attachments/assets/eea083e8-7853-4795-9fdc-fb96d5a766aa" />


---

# Understanding Bridged Networking

Bridged networking makes a VM behave like:

- separate physical device
- same LAN participant
- independently reachable system

This is commonly used for:

- server labs
- Active Directory environments
- remote administration
- infrastructure testing

---

# Checking Remote Desktop Configuration

Remote Desktop settings were reviewed:

```text
Settings
→ System
→ Remote Desktop
```

Initially:

```text
Remote Desktop OFF
```

<img width="797" height="627" alt="rdp-disabled" src="https://github.com/user-attachments/assets/f38e0a2d-48a1-4bc4-a461-170678f63bf2" />


When disabled:

Windows does not:

- accept remote sessions
- listen on RDP port
- allow remote desktop connections

---

# Enabling Remote Desktop

Remote Desktop was enabled.

Windows automatically:

- enabled remote desktop functionality
- configured firewall access
- prepared RDP listener

<img width="796" height="627" alt="rdp-enabled" src="https://github.com/user-attachments/assets/6d9de423-14e9-40d0-813f-9f174a597287" />


---

# Understanding Listeners

Network services commonly wait for traffic using:

```text
listeners
```

A listener means:

> a process waiting for network connections.

RDP uses:

```text
TCP 3389
```

This behaves similarly to:

- SSH daemon
- web server ports
- database listeners

No listener means:

no connection possible.

---

# Verifying RDP Listener

Listener status was validated using:

```cmd
netstat -an | find "3389"
```

The output showed:

```text
LISTENING
```

<img width="592" height="92" alt="rdp-port-listening" src="https://github.com/user-attachments/assets/d1768d12-0571-411f-b1b0-7daa92fdcceb" />


This confirmed:

- RDP service active
- Windows waiting for connections
- TCP 3389 open

---

# Testing Working Remote Access

From the Windows 11 host:

```text
mstsc
```

was launched and connected to:

```text
VM_IP
```

Remote Desktop connected successfully.

<img width="987" height="747" alt="rdp-working" src="https://github.com/user-attachments/assets/be655fe8-003c-4492-8d92-a52ac9a677fb" />


This confirmed:

- network working
- listener active
- firewall permitting access
- credentials accepted

---

# Simulating RDP Failure

Remote Desktop was intentionally disabled.

<img width="797" height="627" alt="rdp-disabled" src="https://github.com/user-attachments/assets/2ab19685-f6ec-413e-ab70-cba9bdd7f6f1" />


This recreated a realistic support scenario.

When disabled:

Windows stopped accepting:

- inbound RDP
- remote desktop sessions

---

# Observing Failure

Connection was attempted again:

```text
mstsc
```

The connection failed.

<img width="682" height="277" alt="rdp-connection-failed" src="https://github.com/user-attachments/assets/3e984725-0543-4b53-b3a0-1054918f267e" />


This demonstrated:

remote access unavailable
despite:

- powered-on VM
- working ping
- reachable network

---

# Investigating Port Availability

Listener status was checked again:

```cmd
netstat -an | find "3389"
```

This time:

no listening port appeared.

<img width="387" height="96" alt="rdp-not-listening" src="https://github.com/user-attachments/assets/3172257a-6acf-457b-8c1b-dab1c1db8ed1" />


This confirmed:

- network operational
- RDP listener absent
- remote service unavailable

---

# Understanding Listener Failure

This introduced another troubleshooting distinction.

| Symptom | Layer |
|---|---|
| Ping failure | Network |
| Timeout | Firewall |
| Missing 3389 listener | Service/configuration |

The same:

```text
Cannot connect
```

symptom may originate from completely different causes.

---

# Restoring Remote Access

Remote Desktop was re-enabled.

Listener validation:

```cmd
netstat -an | find "3389"
```

again showed:

```text
LISTENING
```

Connection through:

```text
mstsc
```

succeeded successfully.

<img width="792" height="622" alt="rdp-restored" src="https://github.com/user-attachments/assets/dad4c94f-3b20-4570-bf42-fcfd2e235b94" />


---

# Key Concepts Learned

| Concept | Explanation |
|---|---|
| RDP | Windows remote desktop protocol |
| TCP 3389 | RDP listening port |
| Listener | Service waiting for traffic |
| NAT | Network isolation model |
| Bridged | Direct LAN participation |
| Layered troubleshooting | Diagnose lower layers first |

---

# Real-World Relevance

Remote Desktop issues commonly occur in:

- enterprise support
- desktop administration
- helpdesk operations
- remote workforce support
- infrastructure management

Typical tickets include:

- unable to remote in
- RDP unavailable
- workstation inaccessible
- listener disabled
- firewall or network problems

---

# Lessons Learned

- Remote access depends on multiple infrastructure layers
- Network reachability should be verified before application troubleshooting
- NAT and bridged networking behave differently
- RDP depends on TCP 3389 listener availability
- Windows Remote Desktop requires compatible editions
- `netstat` helps validate listener presence
- Working ping does not guarantee working remote access
- Troubleshooting should isolate each layer independently
