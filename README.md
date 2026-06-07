# IKB21403 Vulnerability Analysis – Chapter 5 Enumeration

## Student Information

* Name: [Your Name]
* Student ID: [Your Student ID]
* Course: IKB21403 Vulnerability Analysis
* Chapter: Chapter 5 – Enumeration

---

# Objective

The objective of this lab is to perform enumeration activities against a vulnerable target machine to identify open ports, running services, operating system information, network services, and accessible resources.

The attacker machine used was Kali Linux and the victim machine was Metasploitable 2 running within a VMware virtual network.

---

# Lab Environment

## Attacker Machine

* Kali Linux
* IP Address: 192.168.87.132

## Victim Machine

* Metasploitable 2
* IP Address: 192.168.87.133

## Network Discovery

The local network was identified using Nmap host discovery.

### Command

```bash
sudo nmap -sn -n 192.168.87.0/24
```

### Screenshot

![Network Discovery](images/Network%20discovery.png)

### Result

Active hosts discovered:

* 192.168.87.1
* 192.168.87.132 (Kali Linux)
* 192.168.87.133 (Metasploitable 2)
* 192.168.87.254

---

# Challenge 2 – Fast Nmap Scan

## Command

```bash
nmap -F -n 192.168.87.133
```

## Screenshot

![Fast Nmap Scan](images/fast%20nmap%20scan.png)

## Result

The scan identified multiple open ports including:

* FTP (21)
* SSH (22)
* SMTP (25)
* DNS (53)
* HTTP (80)
* SMB (139,445)
* NFS (2049)
* MySQL (3306)
* PostgreSQL (5432)

## Conclusion

The target exposes multiple network services which may be used for further enumeration.

---

# Challenge 4 – SNMPwalk

## Command

```bash
snmpwalk -v1 -c public 192.168.87.133
```

## Screenshot

![SNMPwalk](images/SNMAPWALK.png)

## Verification

```bash
nmap -sU -p161 -n 192.168.87.133
```

## Screenshot

![SNMP Verification](images/snmp-verification.png)

## Result

SNMP enumeration returned no response.

Port scan verification showed:

```text
161/udp closed snmp
```

## Conclusion

SNMP enumeration was unsuccessful because the SNMP service was not running or accessible on the target machine.

---

# Challenge 5 – TTL OS Fingerprinting

## Command

```bash
ping -c 4 192.168.87.133
```

## Screenshot

![TTL OS Fingerprinting](images/TTL%20OS%20Fingerprinting.png)

## Result

The target responded with:

```text
TTL=64
```

## Conclusion

TTL value 64 commonly indicates a Linux or Unix operating system.

---

# Challenge 7 – SMTP VRFY / EXPN

## Command

```bash
nc 192.168.87.133 25
```

Commands issued:

```text
VRFY root
EXPN root
```

## Screenshot

![SMTP VRFY EXPN](images/SMTP%20VRFY%20EXPN.png)

## Result

The SMTP server identified itself as:

```text
Postfix (Ubuntu)
```

Responses received:

```text
252 2.0.0 root
502 5.5.2 Error: command not recognized
```

## Conclusion

The SMTP service responded to VRFY requests but did not support EXPN enumeration.

---

# Challenge 9 – FTP Banner

## Command

```bash
nc 192.168.87.133 21
```

## Screenshot

![FTP Banner](images/FTP%20Banner.png)

## Result

Banner returned:

```text
220 (vsFTPd 2.3.4)
```

## Conclusion

The FTP service version was successfully identified as vsFTPd 2.3.4.

---

# Challenge 10 – Anonymous FTP Login

## Command

```bash
ftp 192.168.87.133
```

Login credentials:

```text
Username: anonymous
Password: anonymous
```

## Screenshot

![Anonymous FTP Login](images/Anonymous%20FTP%20Login.png)

## Result

Anonymous authentication succeeded.

```text
230 Login successful
```

## Conclusion

The FTP server allows anonymous access.

---

# Challenge 11 – SMB NSE Enumeration

## Command

```bash
nmap -n --script smb-os-discovery -p445 192.168.87.133
```

## Screenshot

![SMB NSE Enumeration](images/SMB%20NSE%20Enumeration.png)

## Result

Discovered information:

* OS: Unix
* Samba Version: 3.0.20-Debian
* Computer Name: metasploitable
* Domain Name: localdomain

## Conclusion

SMB enumeration successfully revealed operating system and host information.

---

# Challenge 13 – NFS Exports

## Command

```bash
showmount -e 192.168.87.133
```

## Screenshot

![NFS Exports](images/NFS%20Exports.png)

## Result

The server exported:

```text
/ *
```

## Conclusion

The NFS service exports the root directory to all hosts.

---

# Challenge 16 – Version Detection

## Command

```bash
nmap -sV -n 192.168.87.133
```

## Screenshot

![Version Detection](images/Version%20Detection.png)

## Result

Service versions identified include:

* vsFTPd 2.3.4
* OpenSSH 4.7p1
* Postfix SMTP
* Apache 2.2.8
* Samba 3.x–4.x
* MySQL 5.0.51
* PostgreSQL 8.3

## Conclusion

Version detection successfully identified software running on the target system.

---

# Challenge 17 – OS Detection

## Command

```bash
sudo nmap -O -n 192.168.87.133
```

## Screenshot

![OS Detection](images/OS%20Detection.png)

## Result

Operating system identified as:

```text
Linux 2.6.x
```

## Conclusion

Nmap successfully fingerprinted the target operating system as Linux.

---

# Challenge 19 – RPC Information

## Command

```bash
rpcinfo -p 192.168.87.133
```

## Screenshot

![RPC Info](images/RPC%20Info.png)

## Result

Discovered RPC services:

* portmapper
* status
* NFS
* mountd
* nlockmgr

## Conclusion

RPC enumeration successfully identified multiple network services associated with NFS.

---

# Overall Findings

The target machine exposed numerous services including FTP, SSH, SMTP, HTTP, SMB, RPC, NFS, MySQL, PostgreSQL, and VNC.

Key findings include:

* Anonymous FTP login enabled
* SMB information disclosure
* NFS exports accessible
* SMTP user verification responses
* Multiple outdated software versions
* Linux operating system identified

---

# Conclusion

Enumeration activities successfully identified open ports, active services, software versions, operating system details, SMB information, NFS exports, and RPC services on the Metasploitable 2 target. These findings demonstrate how publicly accessible services can reveal valuable information that may assist further security assessment and vulnerability analysis.
