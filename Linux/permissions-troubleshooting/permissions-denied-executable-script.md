# Linux permission denied troubleshooting for executable scripts

## Objective
The purpose of this lab was to understand how Linux file permissions affect script execution and troubleshoot a common "Permission denied" error when attempting to execute a shell script.

This lab helped reinforce:
- Linux permission fundamentals
- executable file behaviour
- shell script execution
- permission investigation techniques
- Linux security concepts

---

### Environment 
- Guest OS: Ubuntu
- Shell: Bash
- Hypervisor: Virtualbox

---

### Creating the script file
A shell script named `backup.sh` was created using the Nano text editor:

```bash
nano backup.sh
```

The following content was added:

```bash
#!/bin/bash

echo "Backup script executed successfully"
```  

<img width="1280" height="97" alt="script-created" src="https://github.com/user-attachments/assets/8df364c4-3387-4003-9697-10dc14f01063" />  

### Understanding the Shebang

The line:

```bash
#!/bin/bash
```

is called a **shebang**.

It tells Linux which interpreter should execute the script.

In this case:
- `/bin/bash` is the Bash shell interpreter
- Linux passes the script to Bash for execution

This is commonly used in:
- automation scripts
- backup scripts
- deployment tools
- system administration tasks

---

### Attempting to execute the script

The script was executed using:

```bash
./backup.sh
```

However, Linux returned:

```text
Permission denied
```
<img width="325" height="57" alt="permission-denied-error" src="https://github.com/user-attachments/assets/ff225189-fa7f-4d0d-a6c0-86858538c737" />  

### Understanding the error

Initially, the file existed correctly and contained valid Bash commands.

However, Linux still refused execution because the file did not have execute permissions enabled.

This demonstrated an important Linux concept:

> File existence does not automatically mean the operating system allows execution.

Linux separates permission into:
- read
- write
- execute

for different users and groups:

---

### Investigation file permissions

File permissions were inspected using:

```bash
ls -l
```

<img width="426" height="71" alt="file-permissions-before" src="https://github.com/user-attachments/assets/2711a654-4a5b-487a-929f-206c45737a7c" />  

The file showed permission:

```text
-rw-rw-r--
```

### Permission Breakdown

| Section | Meaning |
| --- | --- |
| '-' | Regular file |
| 'rw-' | Owner can read/write |
| 'rw-' | Group can read/write |
| 'r--' | Others can read |

The important observation was: 
- no `x` permission existed

This meant Linux did not allow the file to be executed as a program or script.

---

### Adding execute permission

Execute permission was added using:

```bash
chmod +x backup.sh
```

### Understandin chmod

`chmod` stands for:

```text
change mode
```

It modifies Linux file permission settings.

The `+x` option specifically adds:
- executable permission

to the file.

---

### Verifying updated permissions

Permissions were checked again using:

```bash
ls -l
```

<img width="422" height="87" alt="file-permissions-after" src="https://github.com/user-attachments/assets/2536a65a-256c-4307-a6de-49c1c251f1cf" />  

The file permission now appeared like this:

```text
-rwxrwxr-x
```

### Important observation

The `x` permission was now present, meaning Linux now allows the file to be treated as an executable script.

---

### Executing the script successfully

The script was executed again:

```bash
./backup.sh
```

This time the script executed successfully and displayed:

```text
Backup script executed successfully
```

<img width="292" height="56" alt="successful-script-execution" src="https://github.com/user-attachments/assets/21dfc0ff-8218-406d-8926-53ef57bc2bb9" />  

## Key concepts learned

| Concept | Explanation |
| --- | --- |
| `./script.sh` | Executes a script from the current directory |
| `chmod +x` | Adds execute permission |
| `ls -l` | Displays file permissions and metadata |
| `x` permission | Allows Linux to execute a file |
| Shebang  (`#!/bin/bash`) | Defined which interpreter should run the script |

---

## Real world relevance

Permission-related are extremely common in:
- Linux servers
- automation scripts
- DevOps pipelines
- Docker containers
- deployment systems
- backup jobs
- cron tasks

Common real world examples include:
- deployment scripts failing to execute
- backup automation breaking
- container startup scripts failing
- CI/CD pipeline execution errors

---

# Lessons learned

- Linux treats execution permission separately from file existence
- Scripts are not automatically executable after creation
- The `x` permission is required for script execution
- `chmod` modifies filesystem permission metadata
- `ls -l` is essential for troubleshooting Linux permission issues
- Understanding Linux permissions is foundational for:
  - system administration
  - server management
  - automation
  - cybersecurity

---




