## Procedures
### DFIR
#### look at services:
- `services.msc`
- `net start (get running services)`
- `sc query | more or sc query <servic name>`
- `tasklist /svc`
- WMI `get-wmiobject -query "select * from win32_service`
    `svc_name = "<svc_name>"; $service = get-wmiobject -query "select * from win32_service where name='$svc_name'"; echo $service | fl * > c:\temp\service-wmi-details-$svc_name.txt`
- Powershell `get-service`

#### find newly created accounts with admni rights (persistence)
- list admins `net localgroup administrators`
- find most recent created account in event logs `Get-WinEvent Security | Where-object Id -eq '4720' | fl TimeCreated, Message`