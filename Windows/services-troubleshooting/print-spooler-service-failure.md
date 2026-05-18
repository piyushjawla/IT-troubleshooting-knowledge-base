# Windows Print Spooler Service Troubleshooting

## Objective

The purpose of this lab was to understand how Windows services operate and troubleshoot a service-related printing failure by intentionally stopping and restoring the Print Spooler service.

This lab helped reinforce:
- Windows service architecture
- service state troubleshooting
- command-line service inspection
- background process management
- layered troubleshooting methodology

---

## Environment

- OS: Windows 10
- Tooling:
  - services.msc
  - sc (Service Controller)
- Service:
  - Print Spooler

---

## Understanding Windows Services

Windows services are background processed that provide system functionality without requiring users to launch them manually.

Common examples include:

| Service | Purpose |
| --- | --- |
| Print Spooler | Manager print jobs |
| DNS Client | DNS caching and resolution |
| DHCP Client | IP address acquisition |
| Windows Update | OS updates |
| Event Log | System Logging |

These behave similarly to Linux daemons managed by `systemd`.

---

### Inspecting Print spooler service

The Windows Service Manager was opened using:

```text
services.msc
```

The **Print Spooler** service was located and inspected.

The service initially showed:

- Status: Running
- Startup type: Automatic

<img width="1012" height="625" alt="spooler-running" src="https://github.com/user-attachments/assets/e1e78887-491b-4e4a-a0c1-5ebd86861d5f" />  

---

## Understanding the Print Spooler

The Print Spooler manager print jobs by:

1. receiving print requests
2. queuing jobs
3. scheduling order
4. communicating with printers

This allows Windows to manage printing asynchronously instead of applications communicating directly with printers.

The service properties were reviewed.  

<img width="397" height="466" alt="spooler-properties" src="https://github.com/user-attachments/assets/c5bef242-f79d-4165-a5af-d0d3c9d5a5a0" />  

---

## Inspecting service using command line

The service was inspected using the Windows service controller:

```cmd
sc query spooler
```
<img width="622" height="271" alt="sc-query-running" src="https://github.com/user-attachments/assets/2f8def2e-8c92-4b2e-affc-1fc8cc451e70" />  

### Understanding `sc` 

`sc` stands for:

```text
Service Controller
```

It is used to:
- inspect services
- start services
- stop services
- configure service behaviour

The command showed:

```text
STATE: RUNNING
```

confirming that the service was active.

---

## Simulating service failure

To intentionally recreate a realistic support scenario, the Print Spooler service was stopped.

```cmd
sc stop spooler
```

This simulated conditions similar to:
- crashed services
- update-related failures
- service interruptions

---

## Verifying stopped state

The service state was checked again:

```cmd
sc query spooler
```

The output showed:

```text
STATE: STOPPED
```

<img width="647" height="336" alt="spooler-stopped" src="https://github.com/user-attachments/assets/b1dead61-c4bd-419c-9f0f-aae175a4191c" />   

---

## Observing user symptoms

After stopping the service, printing-related functionality was tested.

Symptoms included:
- unavailable printing funtionality
- inability to process print jobs
- printer-related interface issues

<img width="455" height="416" alt="printing-symptom" src="https://github.com/user-attachments/assets/1dd1da0d-dcdb-492b-a246-69ddced4627a" />  

---

## Troubleshooting investigation

The investigation focused on determining whether:

- printer hardware failed
- drivers failed
- or service availability cause the issue

The service state was reviewed again using:

```cmd
sc query spooler
```

and:

```text
services.msc
```

This confirmed that the Print spooler service itself was unavailable.

---

## Restoring service

The service was restored using:

```cmd
sc start spooler
```

The service successfully returned to:

```text
STATE: RUNNING
```
<img width="637" height="382" alt="spooler-restored" src="https://github.com/user-attachments/assets/9ac7a207-f0f7-43ed-b108-a725037bf326" />  

---

## Final validation

Printing functionality was tested again.

Normal behaviour returned after service restoration.  

<img width="456" height="412" alt="printing-restored" src="https://github.com/user-attachments/assets/2b0162bd-6b2f-4922-a1d1-dc98fc556f79" />  

---

# Key concepts learned

| Concept | Explanation |
| --- | --- |
| Windows service | Background system process |
| services.msc | GUI service management console |
| sc query | service inspection |
| sc stop | stops service |
| sc start | starts service |
| Print Spooler | Print queue management service |

---

# Real world relevance

Print Spooler problems are common in:

- enterprise IT support
- office environments
- desktop administration
- shared printer infrastructure

Typical symptoms include:

- printers disappearing
- stuck print queues
- failed print jobs
- update-related printing failures

---

# Lessons learned

- System functionality often depends on background services
- Hardware failure and service failure are different troubleshooting layers
- Windows services can be inspected through:
  - GUI tools
  - command line tools
- `sc` is an important Windows administration utility
- Effective troubleshooting requires validating service state before assuming hardware or network failure








  
