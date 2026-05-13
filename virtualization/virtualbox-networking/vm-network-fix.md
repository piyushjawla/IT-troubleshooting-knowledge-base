## Problem
The virtual machine could not access the internet despite the host machine having a working internet.

## Environment
- Host OS: Windows 11
- Hypervisor: Virtualbox
- Guest OS: Ubuntu server 26.04
- Network mode: NAT

## Symptoms
- VM could not ping external websites
- `apt update` failed
- DNS resolution was inconsistent

# Initial Hypothesis

Initially, I suspected the VM network adapter configuration was broken because the guest machine had inconsistent internet behavior.

# Validation Tests

To isolate the issue, I tested:

- IP connectivity using ICMP
- DNS resolution separately
- VM adapter settings inside VirtualBox

## Investigation steps
### Step 1 - Verify Host Connectivity
Confirmed the host system had internet access  
<img width="731" height="297" alt="host-internet-works" src="https://github.com/user-attachments/assets/16aedef7-b821-4f2f-bfcc-f6b24506ddb0" />

### Step 2 - Check VM IP address
Used: `ip a`   
<img width="832" height="260" alt="vm-valid-ip" src="https://github.com/user-attachments/assets/44544ff7-d2f8-4fb2-aeb2-809bc81ee51f" />  
Verified the VM has received an IP address.

### Step 3 - Test connectivity
Used: `ping 8.8.8.8`  
<img width="552" height="195" alt="ping8 8 8 8-works" src="https://github.com/user-attachments/assets/f25c6ea6-009c-445f-b2f4-eae5d5e6186d" />   
Ping to Google DNS succeeded

Attempted: `ping google.com`  
<img width="441" height="35" alt="pinggoogle com-fails" src="https://github.com/user-attachments/assets/3c26cea5-37d0-433b-ae5d-6af023a352fd" />  
This failed.

## Root cause
The issue was caused by incorrect DNS configuration inside the guest VM.

## Solution
Updated the `/etc/resolv.conf`:  
<img width="1277" height="801" alt="updated-resolv conf-bash" src="https://github.com/user-attachments/assets/29740c82-cd04-4fec-8028-7979f89a1754" />  

Verified internet connectivity:  
<img width="795" height="197" alt="ping-google com-succeeds" src="https://github.com/user-attachments/assets/d9928d07-cb47-4c4b-b225-11f08879e70f" />  
Ping to google.com succeeds

## Lessons learned
- Successful IP connectivity does not guarantee DNS functionality.
- NAT networking can still fail if guest DNS settings are misconfigured.
- Separating network layer problems from DNS problems improves troubleshooting efficiency

Initially I suspected the VirtualBox NAT adapter itself was broken because the VM had intermittent connectivity. However, testing ICMP connectivity directly to 8.8.8.8 showed packets were leaving successfully, which shifted investigation toward DNS resolution.
