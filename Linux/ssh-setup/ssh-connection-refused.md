# SSH Connection Refused Troubleshooting in Ubuntu

## Objective

The purpose of this lab was to understand how SSH services operate in Linux environments and troubleshoot a real-world "Connection Refused" error while testing remote access between a Windows host and an Ubuntu virtual machine.

This lab also helped reinforce the difference between:
- network reachability
- service availability
- firewall behavior
- daemon/service state

---

## Environment

- Host OS: Windows 11
- Hypervisor: VirtualBox
- Guest OS: Ubuntu
- SSH Server: OpenSSH
- Connection Method: SSH from Windows host to Ubuntu VM

---

### Installing OpenSSH Server

The OpenSSH server package was installed using:

```bash
sudo apt update
sudo apt install openssh-server -y
```

---

### Initial Service Observation

After installation, the SSH service status was checked using:

```bash
sudo systemctl status ssh
```

Unexpectedly, the service appeared as:

```text
inactive (dead)
```  
<img width="687" height="125" alt="initial-ssh-inactive" src="https://github.com/user-attachments/assets/04242750-2ed9-467b-807a-a688643a9b48" />  


### Observation

Initially, I assumed that installing the OpenSSH package would automatically start the SSH daemon. However, the service remained inactive after installation.

This highlighted an important Linux administration concept:

> Successful package installation does not always guarantee that the associated service is actively running.

---

### Reviewing SSH Service Logs

To investigate further, service logs were inspected using:

```bash
sudo journalctl -u ssh --no-pager
```

This step helped reinforce the importance of checking service status and logs before attempting troubleshooting actions.

---

### Starting the SSH Service Manually

The SSH service was started manually using:

```bash
sudo systemctl start ssh
```

The service status was then verified again:

```bash
sudo systemctl status ssh
```

This time, the SSH daemon showed:

```text
active (running)
```  
<img width="731" height="380" alt="ssh-manually-started" src="https://github.com/user-attachments/assets/8f7c1c36-ced2-46e1-95c0-3230441fa491" />  


---

### Identifying the VM IP Address

The VM IP address was identified using:

```bash
ip a
```  
<img width="840" height="257" alt="vm-ip-address" src="https://github.com/user-attachments/assets/62a9c1b9-8ac6-4beb-8c65-4f20d914c656" />  


---

### Testing SSH Connectivity

From the Windows host machine, SSH connectivity was tested using:

```bash
ssh username@VM_IP
```

The SSH login succeeded successfully after accepting the host fingerprint.  
<img width="877" height="841" alt="successful-ssh-login" src="https://github.com/user-attachments/assets/287d3da5-72b6-452b-9ae5-d48a9b02dcd9" />

---

### Simulating SSH Failure

To intentionally recreate a common remote administration issue, the SSH service was stopped manually inside the Ubuntu VM:

```bash
sudo systemctl stop ssh
```  

---

### Reproducing the Error

After stopping the service, another SSH connection attempt was made from the Windows host machine:

```bash
ssh username@VM_IP
```

This time the connection failed with:

```text
Connection refused
```  
<img width="671" height="47" alt="connection-refused-error" src="https://github.com/user-attachments/assets/e0bf99b1-d4f9-4943-8662-25e5a0cac018" />  

---

### Investigation Phase

At this point, it was important to determine whether the issue involved:
- network connectivity
- routing
- firewall behavior
- or the SSH service itself

---

### Verifying Network Reachability

The VM was still reachable using ICMP ping requests:

```bash
ping VM_IP
```  
<img width="621" height="252" alt="vm-reachable-ping" src="https://github.com/user-attachments/assets/4e2a3807-5aaf-439c-aad4-e28964378d15" />  


### Observation

Because the VM still responded to ping requests, this indicated:
- the VM was still online
- network connectivity was functioning
- routing was working correctly

This suggested the issue was likely related specifically to the SSH service rather than general network failure.

---

### Verifying SSH Service State

The SSH service status was checked again:

```bash
sudo systemctl status ssh
```

The service showed:

```text
inactive (dead)
```

This confirmed that the SSH daemon itself was no longer listening for incoming connections.

---

# Restoring SSH Connectivity

The SSH service was restarted using:

```bash
sudo systemctl start ssh
```

The service status was verified again:

```bash
sudo systemctl status ssh
```  
<img width="737" height="321" alt="ssh-restored" src="https://github.com/user-attachments/assets/45de7867-bcd0-4774-8c98-d71b162716aa" />  

---

### Final Validation

A final SSH connection attempt from the Windows host machine succeeded successfully.  
  
<img width="862" height="742" alt="ssh-working-again" src="https://github.com/user-attachments/assets/467d9470-834c-4539-a310-ce69d794c9c0" />  

---

## Key Troubleshooting Concepts Learned

| Observation | Meaning |
|---|---|
| Ping works but SSH fails | Network is reachable but service may be unavailable |
| Connection Refused | Target reachable but service not listening |
| inactive (dead) | Service installed but not running |
| systemctl status | Used to inspect Linux service state |
| journalctl | Used to inspect service logs |

---

# Lessons Learned

- Installing a package does not always mean the associated service is running
- SSH connectivity depends on both:
  - network connectivity
  - service availability
- A system can remain reachable over the network even when specific services fail
- "Connection refused" usually indicates:
  - the target host is reachable
  - but the requested service is not accepting connections
- Linux service management with `systemctl` is a critical sysadmin skill
- Troubleshooting should follow a layered approach:
  - connectivity
  - routing
  - service state
  - firewall behavior
  - application availability
