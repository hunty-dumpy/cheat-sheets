## Procedures
### DFIR
#### Look at services:
- `services.msc`
- `net start (get running services)`
- `sc query | more or sc query <servic name>`
- `sc queryex $svc_name`
- `tasklist /svc`
- WMI `get-wmiobject -query "select * from win32_service`
    `svc_name = "<svc_name>"; $service = get-wmiobject -query "select * from win32_service where name='$svc_name'"; echo $service | fl * > c:\temp\service-wmi-details-$svc_name.txt`
- Powershell `get-service`

#### Find newly created accounts with admni rights (persistence)
- list admins `net localgroup administrators`
- find most recent created account in event logs `Get-WinEvent Security | Where-object Id -eq '4720' | fl TimeCreated, Message`


### Attack
#### Pivoting
- If you SSH'd into a host check `ifconfig` to see what ranges to scan are avaible on other interphases
- If nmap is not present use `nc` for scanning

