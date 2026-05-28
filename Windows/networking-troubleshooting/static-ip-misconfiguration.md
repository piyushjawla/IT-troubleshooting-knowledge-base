# Windows Network Troubleshooting

## Objective

The purpose of this lab was to understand how IP addressing and DHCP function in Windows and troubleshoot a network failure caused by incorrect static IP configuration.

This lab helped reinforce:

- Windows networking fundamentals
- DHCP and static IP configuration
- gateway concepts
- DNS troubleshooting
- connectivity testing
- layered troubleshooting methodology

---

## Environment

- OS: Windows
- Network configuration:
  - IPv4
  - DHCP
  - Static IP testing
 
---

## Understanding IP Addressing

Devices communicate using IP addresses

An IP address acts as a network identity and allows devices to exchange data.

Example:

```text
192.168.1.x
```

Without proper IP addressing:

- devices cannot communicate
- routing fails
- internet access may stop functioning

---

## Understanding DHCP

Most home and office networks use:

```text
DHCP
```

(Dynamic Host Configuration Protocol)

DHCP automatically assigns:

- IP address
- subnet mask
- default gateway
- DNS servers

This removes the need for manual network configuration.

---

## Inspecting current network configuration

Network settings were inspected using:

```cmd
ipconfig /all
```

<img width="700" height="527" alt="working-ipconfig" src="https://github.com/user-attachments/assets/62aa5fb8-d885-40b2-a0ef-fc8d2a9105f1" />  

This displayed:

- IPv4 address
- subnet mask
- gateway
- DNS
- DHCP status

---

## Understanding default gateway

The:

```text
Default Gateway
```

functions as the network exit path.

It allows devices to:

- leave local networks
- reach external networks
- access internet resources

Without a valid gateway:

local communication may work,
but internet access fails.

---

## Validating working connectivity

Connectivity was tested before making changes.

Gateway reachability:

```cmd
ping 10.0.3.2
```

Internet routing:

```cmd
ping 8.8.8.8
```

DNS resolution:

```cmd
ping google.com
```

<img width="480" height="622" alt="working-connectivity" src="https://github.com/user-attachments/assets/c19bbe58-f383-4f25-881d-ef55f62fbe24" />  


---

## Understanding layered connectivity tests

These tests isolate different networking layers.

| Test | Purpose |
| --- | --- |
| Gateway ping | Local network reachability |
| ping 8.8.8.8 | Internet routing |
| ping google.com | DNS resolution |

This methodology helps isolate failures accurately.

---

## Reviewing adapter configuration

Windows adapter settings were opened:

```cmd
ncpa.cpl
```

The active adapter showed:

- Obtain IP automatically
- Obtain DNS automatically

<img width="392" height="450" alt="dhcp-enabled" src="https://github.com/user-attachments/assets/9f2c41f1-a150-4712-8aec-1598c3865190" />  

This confirmed DHCP operation.

---

## DHCP vs Static configuration

| DHCP | Static |
| --- | --- |
| Automatic | Manual |
| Common for endpoints | Common for infrastructure |
| Easy to manage | Requires precision |

Static addressing is often used for:

- servers
- printers
- infrastructure devices

DHCP is common for:

- laptops
- desktops
- end-user devices

---

## Simulating misconfiguration

To recreate a realistic networking issue, DHCP was disabled and incorrect static settings were configured.

```text
IP: 10.10.10.15
Subnet: 255.0.0.0
Gateway: 10.10.10.1
DNS: 8.8.8.8
```

<img width="397" height="456" alt="bad-static-ip" src="https://github.com/user-attachments/assets/f4bcc584-fe29-4290-b880-dea132573b15" />  

---

## Why misconfiguration causes failure

The actual network used a different subnet.

Windows therefore:

- stopped requesting DHCP
- trusted manual settings
- assumed incorrect network location

This prevented communication with the real gateway.

---

## Observing failure

After applying incorrect settings:

```cmd
ipconfig
```

Connectivity tests failed:

```cmd
ping gateway
ping 8.8.8.8
ping google.com
```

<img width="670" height="657" alt="connectivity-failure" src="https://github.com/user-attachments/assets/baff69f1-57d9-446e-920d-3ed100839e84" />  

Observed symptoms include:

- unreachable gateway
- failed internet access
- failed DNS resolution

---

## Investigating the problem

Configuration was inspected again using:

```cmd
ipconfig /all
```

<img width="700" height="482" alt="misconfigured-ip" src="https://github.com/user-attachments/assets/9e62a086-f378-49b3-a623-d79189bc06e9" />  

The output confirmed:

- DHCP disabled
- manual configuration active
- incorrect addressing

This helped identify the root cause.

---

## Restoring DHCP

Automatic configuration was restored:

- Obtain IP automatically
- Obtain DNS automatically

The network lease was refreshed:

```cmd
ipconfig /release
ipconfig /renew
```

<img width="627" height="467" alt="dhcp-restored" src="https://github.com/user-attachments/assets/150a3eb5-9a77-4c04-90ea-c3e60978ab71" />  

---

## Understanding release and renew

Windows performs:

### Release

```cmd
ipconfig /release
```

This: 

- drops current lease
- removes assigned IP

### Renew 

```cmd
ipconfig /renew
```

This:

- requests DHCP configuration
- obtains fresh settings
- restores connectivity

---

## Final validation

Configuration and connectivity were validated:

```cmd
ipconfig /all
```

Connectivity tests:

```cmd
ping gateway
ping 8.8.8.8
ping google.com
```

<img width="495" height="665" alt="restored-connectivity" src="https://github.com/user-attachments/assets/544ad0ee-8e83-4b6c-8c48-a6c5787c8b7f" />  

This confirmed successful recovery.

---

# Key concepts learned

| Concept | Explanation |
| --- | --- |
| DHCP | Automatic IP assignment |
| Static IP | Manual addressing |
| Gateway | Network exit path |
| DNS | Name resolution |
| Subnet | Network boundary |
| Connectivity testing | Layered diagnosis |

---

# Real world relevance

Incorrect IP configuration commonly causes:

- office connectivity failures
- VPN related issues
- endpoint misconfiguration
- remote support tickets
- internet access problems

Typical symptoms include:

- connected but no internet
- failed browsing
- unreachable services
- DNS complaints

--- 

# Lessons learned

- Windows networking depends on correct IP configuration
- DHCP automates network setup
- Incorrect static addressing can break communication completely
- Gateway and DNS serve different purposes
- Connectivity testing should isolate network layers individually
- `ipconfig /all` is a powerful troubleshooting tool
- `release` and `renew` help restore DHCP functionality







