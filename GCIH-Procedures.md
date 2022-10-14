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

### Scheduled Tasks
#### Acquire
- use sysinternals autoruns.exe
- PS `get-scheduledTask`
- Grab files from `Windows\Tasks` OR  `Widows\System32\tasks`
#### Remove
-`Unregister-scheduledtask -TaskName "task name" -Confirm:$false`

#### Find newly created accounts with admni rights (persistence)
- list admins `net localgroup administrators`
- find most recent created account in event logs `Get-WinEvent Security | Where-object Id -eq '4720' | fl TimeCreated, Message`


### Defender and SCEP Local Logs
- `%SystemRoot%\System32\Winevt\Logs\Microsoft-Windows-Windows Defender%4Operational.evtx`
- SCEP: `Get-ChildItem "C:\ProgramData\Microsoft\Windows Defender\Support"`
  - Particularly Useful:
    - MPDetection*
    - MPLog*
  - Other possible Locations:
    - Win10 20H2:
      - `c:\ProgramData\Microsoft\Windows Defender\Support\`
      - `c:\Users\All Users\Microsoft\Windows Defender\Support\`
    - Win7 year 2021 scans logs:
      - `c:\ProgramData\Microsoft\Microsoft Antimalware\Support\`
    - Win7 year 2019 scans logs:
      - `c:\ProgramData\Microsoft\Windows Defender\Support\`
    - Other:
      - `C:\Windows\Microsoft Antimalware\Support\`


### Windows Event Logs
#### Acquire
- Locations:
  - `C:\WINDOWS\System32\Winevt\Logs\`
#### Query
- By Log Name and Event ID number:
  - `Get-WinEvent Security | Where-object Id -eq '###' | fl TimeCreated, Message`
### Attack
#### Pivoting
- If you SSH'd into a host check `ifconfig` to see what ranges to scan are avaible on other interphases
- If nmap is not present use `nc` for scanning

