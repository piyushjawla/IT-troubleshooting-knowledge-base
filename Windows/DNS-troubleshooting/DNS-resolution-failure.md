# Windows DNS Resolution Troubleshooting

## Objective

The purpose of this lab was to understand how DNS resolution works in Windows environments and troubleshoot a DNS-related connectivity issue between:
- internet connectivity
- hostname resolution
- DNS cache behaviour

This lab also reinforced the importance of layered troubleshooting when diagnosing network-related issues.

---

### Environment

- OS: Windows 10
- Tools used:
  - ping
  - ipconfig
  - nslookup
 
---

### Verifying initial connectivity

Initial connectivity test was performed using:

```cmd
ping.google.com
```

The hostname resolved successfully and the system received replies normally.  
<img width="485" height="207" alt="successful-google-ping" src="https://github.com/user-attachments/assets/d39059f5-e635-41e4-9ab1-e76fa74afb60" />  

---

### Testing Raw IP connectivity

Connectivity was also tested directly using Google's public DNS IP address:

```cmd
ping 8.8.8.8
```
<img width="462" height="207" alt="successful-ip-ping" src="https://github.com/user-attachments/assets/d06bfeee-7c11-41f1-8831-57cacd6f3066" />  

### Observation

This test bypassed DNS completely and verified that:
- internet connectivity
- routing
- network access

were functioning correctly.

---

### Reviewing Current DNS configuration

The active network configuration was reviewed using:

```cmd
ipconfig /all
```

The configured DNS server information was identified from the active network adapter.  
<img width="701" height="526" alt="dns-server-config" src="https://github.com/user-attachments/assets/5069996c-8a40-4588-a1b2-db6f232506eb" />  

---

### Testing DNS resolution directly

DNS resolution was tested using:

```cmd
nslookup google.com
```
<img width="311" height="145" alt="successful-nslookup" src="https://github.com/user-attachments/assets/0c3623b5-642e-491d-8050-0b9758249586" />  

### Understanding nslookup

`nslookup` directly queries DNS servers and is commonly used to diagnose:
- DNS failures
- hostname resolution problems
- DNS server availability

This provides more reliable DNS diagnostics than relying only on `ping`.

---

### Simulating DNS failure

To intentionally recreate a DNS issue, the network adapter DNS settings were modified manually.

The configured DNS server was changed to:

```text
127.0.0.1
```
<img width="392" height="447" alt="invalid-dns-config-loopback" src="https://github.com/user-attachments/assets/bec941c6-0a21-4830-86d9-bede43effca1" />  

### Why 127.0.0.1 was used

`127.0.0.1` refers to the local machine itself (localhost).

Because no local DNS server was running on the system, DNS queries could no longer be resolved successfully.

---

### Unexpected behaviour during testing

Initially, even after the DNS configuration was intentionally broken, the system was still able to successfully ping `google.com`.

This behaviour was unexpected at first.  
<img width="402" height="527" alt="dns-cache-entries" src="https://github.com/user-attachments/assets/25cd0086-f77f-4ef2-83ca-94197f0336d7" />  


### Investigation

Further analysis revealed that Windows DNS caching was still temporarily providing previously resolved hostname information.

This demonstrated an important real-world troubleshooting concept:

> Cached DNS records can temporarily mask DNS misconfigurations.

---

### Reviewing DNS Cache

The local DNS cache was inspected using:

```cmd
ipconfig /displaydns
```

This displayed cached hostname-to-IP mappings stored locally by Windows.

---

### Flushing DNS Cache

The DNS cache was cleared using:

```cmd
ipconfig /flushdns
```
<img width="362" height="96" alt="flushdns-success" src="https://github.com/user-attachments/assets/426f920e-e1f1-4a72-b913-bc060d6b25f9" />  


### Understanding DNS cache

Windows stores recently resolved DNS records locally to:
- improve browsing performance
- reduce repeated DNS queries

However, stale or cached entries can complicate troubleshooting and temporarily hide configuration problems. 

---

### Observing DNS failure

After flushing the cache and using the invalid DNS configuration, hostname resolution failed.

```cmd
ping google.com
```
<img width="667" height="46" alt="failed-google-ping" src="https://github.com/user-attachments/assets/dbdc36bf-977e-4420-9305-fb2bd5cd4524" />  

---

### Verifying Internet connectivity still worked

Despite DNS failure, direct IP connectivity continued functioning:

```cmd
ping 8.8.8.8
```
<img width="457" height="211" alt="ip-connectivity-still-working" src="https://github.com/user-attachments/assets/9708b3f3-cd6c-4972-adf3-98c139cb9aaf" />  

### Key observation

This proved:
- internet connectivity still existed
- routing still functioned correctly
- only hostname resolution was failing

This is one of the most important distinctions in network troubleshooting.

---

### Verifying DNS failure with nslookup

DNS resolution was tested again:

```cmd
nslookup google.com
```

This time the query failed.  
<img width="482" height="97" alt="failed-nslookup" src="https://github.com/user-attachments/assets/c90952ff-32b1-418e-95e6-e09b0ebe9b77" />  

---

### Restoring DNS functionality

The DNS settings were restored to automatic configuration.  

<img width="390" height="447" alt="restored-dns-settings" src="https://github.com/user-attachments/assets/10cc1204-be2e-4728-9918-876c46c552db" />  

Connectivity and DNS resolution were tested again successfully.  

<img width="476" height="360" alt="dns-restored-successfully" src="https://github.com/user-attachments/assets/039086e3-3664-48ad-9b7d-bf93b0686e3f" />  


---

## Key concepts learned

| Concept | Explanation |
| --- | --- |
| DNS | Translates hostnames into IP addresses |
| ping hostname | Tests both DNS resolution and connectivity |
| ping IP | Tests connectivity only |
| nslookup | Direct DNS diagnostic tool |
| DNS Cache | Stores previously resolved records locally |
| flushdns | clears cached DNS records |

---

## Real world relevance

DNS-related issues are extremely common in:
- enterprise IT support
- VPN environments
- cloud systems
- Active Directory environments
- internal corporate networks

Common symptoms include:
- websites failing while internet still works
- applications unable to resolve internal servers
- intermittent connectivity issues caused by stale DNS cache
- VPN-related name resolution failures

---

## Lessons learned

- Internet connectivity and DNS resolution are separate networking layers
- Successful IP connectivity does not guarantee hostname resolution
- DNS caching can temporarily hide configuration problems
- `nslookup` is more reliable for DNS troubleshooting than relying on `ping`
- Multiple network adapters and fallback behaviour can complicate troubleshooting
- Effective troubleshooting requires validating assumptions rather than relying on expected behaviour










