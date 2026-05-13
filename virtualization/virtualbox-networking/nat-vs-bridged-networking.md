# NAT vs Bridged Networking in Virtualbox

## Objective  
The purpose of this lab was to understand the differences between NAT and Bridged networking modes in Virtualbox and how they affect internet connectivity, IP addressing, LAN visibility, and communicaiton with the host system.


### Environment  
- Host OS: Windows 11
- Hypervisor: Virtualbox
- Guest OS: Ubuntu

# NAT Networking  
### Configuration  
The VM network adapter was initially configured to use NAT mode in Virtualbox settings.  
<img width="955" height="582" alt="nat-adapter-settings" src="https://github.com/user-attachments/assets/2831f736-1054-4b52-9c29-e27ac55e1f60" />  

### IP address observation  
Inside the Ubuntu VM, I used `ip a` to see the IP address:  
The VM received an IP address in the `10.0.2.x` range, which is commonly used by Virtualbox NAT networking.  
<img width="845" height="267" alt="nat-ip-address" src="https://github.com/user-attachments/assets/eef35197-dae5-4929-82c1-4d8bf15503e5" />    

### Internet connectivity test  
Connectivity testing was done using ICMP ping requests.  
`ping 8.8.8.8`   
The VM successfully reached external internet resources.  
<img width="527" height="192" alt="nat-ping-test" src="https://github.com/user-attachments/assets/119a78f0-16ea-4c2f-b5ac-df0b132acd15" />  

### Host reachability test  
The VM also attempted to ping the Windows host machine using the host LAN IP address.  
Unexpectedly, the host machine responded successfully while the VM was using NAT mode.  
<img width="507" height="177" alt="nat-host-ping-test" src="https://github.com/user-attachments/assets/3bd8443c-17c6-415f-b532-cf718d2ffa67" />  

### Observation  
Initially, I assumed NAT mode would completely isolate the VM from direct communication with the host system. However, Virtualbox NAT networking still allowed communication between guest VM and host machine.  

This highlighted that NAT isolation behaviour can vary depending on:  
- Virtualbox implementation
- Host OS behaviour
- Firewall configuration

# Bridged Networking  

### Configuration   
The VM network adapter was then switched to Bridged adapter mode.  
<img width="955" height="581" alt="bridged-adapter-settings" src="https://github.com/user-attachments/assets/78b117ee-d387-452d-90b9-341679096abd" />  

### IP address observation  
After switching to Bridged mode, the VM received an IP address within the same LAN subnet as the host machine.   
Unlike NAT mode, the VM now seems like an independent device on the local network.  
<img width="845" height="262" alt="bridged-ip-address" src="https://github.com/user-attachments/assets/69ae28fa-f19e-451f-8494-b30dda6878ba" />  

### Internet connectivity test  
Connectivity testing was repeated successfully using `ping 8.8.8.8`  
<img width="507" height="157" alt="bridged-ping-test" src="https://github.com/user-attachments/assets/ff6f4cf2-f624-4d2a-99f3-2f46399d5859" />  

### Gateway connectivity test  
The VM successfully communicated with the local gateway/router.  
This confirmed that bridged networking and LAN connectivity were functioning correctly.  
<img width="511" height="176" alt="bridged-gateway-ping" src="https://github.com/user-attachments/assets/d45ca063-5535-41a1-abbd-985504e0e406" />  

### Host reachability test  
The VM then attempted to ping the host system.  
However, unlike NAT mode, the ping request appeared stuck without receiving replies.  
<img width="537" height="376" alt="bridges-host-ping-fails" src="https://github.com/user-attachments/assets/021ea0ce-a29b-493d-9db9-c9510cb82842" />  

### Investigation  
At first, this behaviour was confusing because bridged networking is generally expected to provide direct LAN communication with devices as they are on the same subnet.  

However, further analysis suggested:  
- the VM was correctly connected to the LAN
- routing was functioning properly
- the gateway was reachable (as seen above in the Gateway connectivity test)
- the issue likely involved Windows Firewall behaviour rather than Virtualbox networking configuration.

### Root cause analysis  
The Windows Firewall configuration blocker inbound ICMP echo requests, which caused the ping command to wait indefinitely for replies.  
Once inbound rule was enabled for the network profile on the Host machine:  
<img width="1302" height="892" alt="firewall-inbound-rule" src="https://github.com/user-attachments/assets/d523f370-6cb1-4c9b-9390-564f6d003f01" />  
The guest VM could now successfully communicate with the host system:  
<img width="531" height="167" alt="bridged-ping-host-success" src="https://github.com/user-attachments/assets/28eabae4-8d99-4fb2-afda-3b335d859bff" />  


# Lesson learned  
- NAT networking does not always completely isolate guest VMs from the host machine
- Bridged networking allows the VM to behave like an independent device on the LAN
- Successful LAN connectivity does not guarantee unrestricted communication between devices
- Firewall policies can affect ICMP communication even when routing is functioning correctly
- Troubleshooting networking issues requires separating:
  - connectivity
  - routing
  - firewall behavior
  - protocol filtering
- Practical labs often reveal behavior that differs from simplified textbook explanations













