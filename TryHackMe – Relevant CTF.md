**Platform:** TryHackMe  
**Room:** Relevant  
**Target IP:** `10.49.164.33`  
**Difficulty:** Medium  
**Focus:** SMB Enumeration, Initial Access, Windows Privilege Escalation

---

## 1. Introduction

In this walkthrough, I will demonstrate my approach to compromising the Relevant machine on TryHackMe.

The assessment involved identifying open ports and services, enumerating SMB shares, discovering encoded credentials, obtaining initial access through a reverse shell, capturing the user flag, and escalating privileges to Administrator to retrieve the final flag.

## 2. Reconnaissance and Enumeration

### Step 1: Checking Target Connectivity

First, I checked whether the target machine was reachable using the `ping` command.

**Command:**

bash

```
ping -c 4 10.49.164.33
```

This helped confirm network connectivity between my attacking machine and the target.

![[{05FF182A-4935-4D26-A5B3-A4B63BC19421}.png]]

### Step 2: Scanning Open Ports

After confirming connectivity, I performed a comprehensive port scan to identify open ports and running services.

**Command:**

bash

```
nmap -sC -sV -p- 10.49.164.33
```

**Explanation:**

- `-sC`: Runs default Nmap scripts.
- `-sV`: Detects service versions.
- `-p-`: Scans all TCP ports.

The scan helped identify the exposed SMB service.

### Step 3: Identifying SMB

After reviewing the scan results, I identified SMB as an exposed service on the target machine.

I decided to investigate the SMB service to identify accessible network shares and potentially sensitive files.

## 3. SMB Enumeration

### Step 4: Connecting to SMB

I used the SMB client utility to connect to the target SMB service.

**Command:**

bash

```
smbclient -L //10.49.164.33/ -N
```

This command attempts to list available SMB shares using a null session.

![[{7FF9E727-F1D4-4ADB-847B-3870DA11759A} 1.png]]

### Step 5: Enumerating SMB Directories

After identifying an accessible share, I connected to it and explored its contents.

**Command:**

bash

```
smbclient //10.49.164.33/nt4wrksv
```

![[Pasted image 20261116160339.png]]

Inside the SMB session, I used:

text

```
ls
```

To navigate directories:

text

```
cd Users
```

To download an accessible file:

text

```
get password.txt
```

### Step 6: Discovering password.txt

During SMB enumeration, I discovered a file named:

text

```
password.txt
```

I downloaded the file for further analysis.

### Step 7: Decoding the Password

After examining the file contents, I identified that the password was encoded using Base64.

I used the following command to decode it:

The decoded output provided the original password.

![[{1D0153F4-23A5-4AE1-96C9-962FFC7F3AE5}.png]]
## 4. Initial Access

### Step 8: Preparing the Reverse Shell

After obtaining the credentials and identifying the accessible SMB share, I prepared a reverse shell payload and transferred it through the SMB share.

I used the following SMB command to upload an authorized lab file:

text

```
put shell.aspx
```

![[{4AB56094-5E12-4357-A595-1E81EC993C59}.png]]
### Step 9: Obtaining Shell Access

I configured my listener using Netcat to receive the incoming connection.

**Listener command:**

bash

```
nc -lvnp 1234
```

After the reverse shell was triggered, I successfully obtained an interactive connection to the target machine.

## 5. User Flag

### Step 10: Enumerating User Directories

After obtaining initial shell access, I started exploring the Windows directory structure and identifying user accounts.

Useful enumeration commands:

cmd

```
whoami
```

cmd

```
hostname
```

cmd

```
dir C:\Users
```

### Step 11: Locating user.txt

I explored the accessible user directories and located the `user.txt` file.

### Step 12: Capturing the First Flag

I used the following command to read the file:

cmd

```
type C:\Users\USERNAME\Desktop\user.txt
```

After reading its contents, I successfully captured the first flag.

![[{EE1F40AB-BEEF-4C70-81FA-10A83E0C25EB}.png]]

## 6. Privilege Escalation

### Step 13: Investigating Administrator Access

After obtaining the user flag, I continued enumerating the system to identify possible privilege escalation opportunities.

I attempted to access the Administrator directory:

cmd

```
dir C:\Users\Administrator
```

However, access was denied because my current account did not have sufficient privileges.

### Step 14: Identifying the Print Spooler Privilege Escalation Opportunity

I investigated the Windows security configuration and identified a potential privilege escalation opportunity involving the Print Spooler service.

I used PrintSpoofer.exe in the authorized lab environment to leverage the relevant impersonation privilege and obtain elevated access.

The important Windows privilege to investigate in this scenario is:

text

```
SeImpersonatePrivilege
```

I verified the current security privileges using:

cmd

```
whoami /priv
```

![[{111815F4-A38A-4E72-B9F0-4CC7977E8DB0}.png]]

![[{92687C16-659C-476E-B5CB-8FC119C4D98A}.png]]

![[{26B83092-C65A-4996-8434-AB8D969EA9D1} 1.png]]

![[{299A4E53-6315-4C0C-B9C9-AFF3E4985532}.png]]
### Step 15: Obtaining Administrator Access and Capturing the Final Flag

After successfully escalating privileges, I obtained Administrator-level access.

I verified the account context:

cmd

```
whoami
```

I then accessed the Administrator directory and located the final flag file.

The final flag was retrieved from:

cmd

```
type C:\Users\Administrator\Desktop\root.txt
```

With this, I successfully captured both flags and completed the Relevant CTF machine.

![[{E062B587-8381-41E7-9090-E469F6FE6466}.png]]

---

```
Flag1:- THM{fdk4ka34vk346ksxfr21tg789ktf45}
Flag2:- THM{1fk5kf469devly1gl320zafgl345pv}
```


## 7. Tools Used

|Tool|Purpose|
|---|---|
|Nmap|Network scanning and service enumeration|
|SMBClient|SMB share enumeration and file access|
|Base64|Decoding encoded credentials|
|Netcat|Receiving reverse shell connections|
|PrintSpoofer|Windows privilege escalation investigation|

## 8. Key Takeaways

Through this challenge, I gained practical experience in:

1. Performing network reconnaissance using Nmap.
2. Enumerating exposed SMB services.
3. Identifying sensitive information in SMB shares.
4. Decoding Base64-encoded credentials.
5. Understanding reverse shell-based initial access.
6. Enumerating Windows users and directories.
7. Understanding Windows impersonation privileges.
8. Learning about Print Spooler-related privilege escalation.
9. Obtaining Administrator-level access.

## 9. Conclusion

The Relevant CTF challenge provided hands-on experience with Windows penetration testing methodologies, from initial reconnaissance to privilege escalation.

By combining network scanning, SMB enumeration, credential discovery, shell access, and Windows privilege escalation techniques, I successfully obtained both the user flag and the final Administrator-level flag.

**Final Result: Successfully completed the TryHackMe Relevant CTF machine.**

---

**Disclaimer:** This walkthrough is intended strictly for educational purposes. All testing and exploitation activities were performed within the authorized TryHackMe lab environment.