| [![Gatekeeper](../../_static/images/gatekeeper-room-banner.png)](https://tryhackme.com/room/gatekeeper) |
|:--:|
| [THM Gatekeeper](https://tryhackme.com/room/gatekeeper) |

# THM Gatekeeper

Can you get past the gate and through the fire?

## Preparation

[Set up a small local lab](../prep/README.md).

## Scanning

    nmap -p- -T4 -Pn $MACHINE_IP
    
    Starting Nmap 7.60 ( https://nmap.org ) at 2022-12-13 13:46 GMT
    Nmap scan report for ip-10-10-110-250.eu-west-1.compute.internal (10.10.110.250)
    Host is up (0.00044s latency).
    Not shown: 65524 closed ports
    PORT      STATE SERVICE
    135/tcp   open  msrpc
    139/tcp   open  netbios-ssn
    445/tcp   open  microsoft-ds
    3389/tcp  open  ms-wbt-server
    31337/tcp open  Elite
    49152/tcp open  unknown
    49153/tcp open  unknown
    49154/tcp open  unknown
    49160/tcp open  unknown
    49161/tcp open  unknown
    49162/tcp open  unknown
    MAC Address: 02:D1:76:28:34:57 (Unknown)
    
    Nmap done: 1 IP address (1 host up) scanned in 1642.77 seconds

## Enumeration

    sudo nmap -p 135,139,445,3389,31337 -sV -sC -v -Pn -T4 $MACHINE_IP

    Starting Nmap 7.60 ( https://nmap.org ) at 2022-12-13 14:17 GMT
    NSE: Loaded 146 scripts for scanning.
    NSE: Script Pre-scanning.
    Initiating NSE at 14:17
    Completed NSE at 14:17, 0.00s elapsed
    Initiating NSE at 14:17
    Completed NSE at 14:17, 0.00s elapsed
    Initiating ARP Ping Scan at 14:17
    Scanning 10.10.110.250 [1 port]
    Completed ARP Ping Scan at 14:17, 0.22s elapsed (1 total hosts)
    Initiating Parallel DNS resolution of 1 host. at 14:17
    Completed Parallel DNS resolution of 1 host. at 14:17, 0.00s elapsed
    Initiating SYN Stealth Scan at 14:17
    Scanning ip-10-10-110-250.eu-west-1.compute.internal (10.10.110.250) [5 ports]
    Discovered open port 31337/tcp on 10.10.110.250
    Completed SYN Stealth Scan at 14:17, 1.24s elapsed (5 total ports)
    Initiating Service scan at 14:17
    Scanning 1 service on ip-10-10-110-250.eu-west-1.compute.internal (10.10.110.250)
    Completed Service scan at 14:20, 146.16s elapsed (1 service on 1 host)
    NSE: Script scanning 10.10.110.250.
    Initiating NSE at 14:20
    Completed NSE at 14:20, 0.01s elapsed
    Initiating NSE at 14:20
    Completed NSE at 14:20, 1.01s elapsed
    Nmap scan report for ip-10-10-110-250.eu-west-1.compute.internal (10.10.110.250)
    Host is up (0.00018s latency).
    
    PORT      STATE    SERVICE       VERSION
    135/tcp   filtered msrpc
    139/tcp   filtered netbios-ssn
    445/tcp   filtered microsoft-ds
    3389/tcp  filtered ms-wbt-server
    31337/tcp open     Elite?
    | fingerprint-strings: 
    |   FourOhFourRequest: 
    |     Hello GET /nice%20ports%2C/Tri%6Eity.txt%2ebak HTTP/1.0
    |     Hello
    |   GenericLines: 
    |     Hello 
    |     Hello
    |   GetRequest: 
    |     Hello GET / HTTP/1.0
    |     Hello
    |   HTTPOptions: 
    |     Hello OPTIONS / HTTP/1.0
    |     Hello
    |   Help: 
    |     Hello HELP
    |   Kerberos: 
    |     Hello !!!
    |   LDAPSearchReq: 
    |     Hello 0
    |     Hello
    |   LPDString: 
    |     Hello 
    |     default!!!
    |   RTSPRequest: 
    |     Hello OPTIONS / RTSP/1.0
    |     Hello
    |   SIPOptions: 
    |     Hello OPTIONS sip:nm SIP/2.0
    |     Hello Via: SIP/2.0/TCP nm;branch=foo
    |     Hello From: <sip:nm@nm>;tag=root
    |     Hello To: <sip:nm2@nm2>
    |     Hello Call-ID: 50000
    |     Hello CSeq: 42 OPTIONS
    |     Hello Max-Forwards: 70
    |     Hello Content-Length: 0
    |     Hello Contact: <sip:nm@nm>
    |     Hello Accept: application/sdp
    |     Hello
    |   SSLSessionReq, TLSSessionReq: 
    |_    Hello
    1 service unrecognized despite returning data. ...
    
    NSE: Script Post-scanning.
    Initiating NSE at 14:20
    Completed NSE at 14:20, 0.00s elapsed
    Initiating NSE at 14:20
    Completed NSE at 14:20, 0.00s elapsed
    Read data files from: /usr/bin/../share/nmap
    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 149.29 seconds
               Raw packets sent: 10 (424B) | Rcvd: 2 (72B)

### Interacting with the service on port 31337

    nc $MACHINE_IP 31337


    Hello !!!
    Hi
    Hello Hi!!!

And with a very long string, gets kicked out.

### SMB

Using `smbclient` to list available shares on the host:

    smbclient -L $MACHINE_IP

    Enter WORKGROUP\root's password: 
    
            Sharename       Type      Comment
            ---------       ----      -------
            ADMIN$          Disk      Remote Admin
            C$              Disk      Default share
            IPC$            IPC       Remote IPC
            Users           Disk      
    SMB1 disabled -- no workgroup available

Using `smbclient` to access the `Users` share anonymously:

    smbclient \\\\$MACHINE_IP\\Users

    Enter WORKGROUP\root's password: 
    Try "help" to get a list of possible commands.
    smb: \> ls
      .                                  DR        0  Thu May 14 21:57:08 2020
      ..                                 DR        0  Thu May 14 21:57:08 2020
      Default                           DHR        0  Tue Jul 14 03:07:31 2009
      desktop.ini                       AHS      174  Tue Jul 14 00:54:24 2009
      Share                               D        0  Thu May 14 21:58:07 2020
                      7863807 blocks of size 4096. 3876715 blocks available

    smb: \> cd Share
    smb: \Share\> ls
      .                                   D        0  Thu May 14 21:58:07 2020
      ..                                  D        0  Thu May 14 21:58:07 2020
      gatekeeper.exe                      A    13312  Mon Apr 20 01:27:17 2020
    
                    7863807 blocks of size 4096. 3876715 blocks available

Getting the `gatekeeper.exe` file:

    smb: \Share\> get gatekeeper.exe
    getting file \Share\gatekeeper.exe of size 13312 as gatekeeper.exe (5.1 KiloBytes/sec) (average 5.1 KiloBytes/sec)

Start a web server on the Kali VM in the directory with the files (to download the files to the Windows VM):

    python3 -m http.server

## Exploiting buffer overflow

Get the binary file from the Kali box and follow the [stack-based buffer overflow howto](../prep/overflow1.md) 
for creating BoF scripts, with:

    Offset: 146
    JMP ESP address: 080414C3
    Bad characters: 00, 0a

Script:

```python
next
```




