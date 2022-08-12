**目录**

[Seatbelt](#t0 "Seatbelt")

[Seatbelt的用法](#t1 "Seatbelt的用法")

[运行所有检查](#t2 "运行所有检查")

[检查系统相关](#t3 "检查系统相关")

[检查用户相关](#t4 "检查用户相关")

[杂项](#t5 "杂项 ") 

* * *

Seatbelt
--------

Seatbelt是一个采用C#写的Windows下信息收集的工具。它从进攻性和防御性安全性角度执行许多面向安全性的主机调查“安全性检查”。

Seatbelt的用法
-----------

```
Available commands (+ means remote usage is supported):      
        + AMSIProviders          - Providers registered for AMSI      
        + AntiVirus              - Registered antivirus (via WMI)      
          AppLocker              - AppLocker settings, if installed      
          ARPTable               - Lists the current ARP table and adapter information (equivalent to arp -a)      
          AuditPolicies          - Enumerates classic and advanced audit policy settings      
        + AuditPolicyRegistry    - Audit settings via the registry      
        + AutoRuns               - Auto run executables/scripts/programs      
          ChromeBookmarks        - Parses any found Chrome bookmark files      1
          ChromeHistory          - Parses any found Chrome history files      1
          ChromePresence         - Checks if interesting Google Chrome files exist      1
          CloudCredentials       - AWS/Google/Azure cloud credential files      1
          CredEnum               - Enumerates the current user's saved credentials using CredEnumerate()      1
          CredGuard              - CredentialGuard configuration      1
          dir                    - Lists files/folders. By default, lists users' downloads, documents, and desktop folders (arguments == [directory] [depth] [regex] [boolIgnoreErrors]      1
        + DNSCache               - DNS cache entries (via WMI)      1
        + DotNet                 - DotNet versions      1
          DpapiMasterKeys        - List DPAPI master keys      1
          EnvironmentPath        - Current environment %PATH$ folders and SDDL information      2
          EnvironmentVariables   - Current user environment variables      2
          ExplicitLogonEvents    - Explicit Logon events (Event ID 4648) from the security event log. Default of 7 days, argument == last X days.      2
          ExplorerMRUs           - Explorer most recently used files (last 7 days, argument == last X days)      2
        + ExplorerRunCommands    - Recent Explorer "run" commands      2
          FileInfo               - Information about a file (version information, timestamps, basic PE info, etc. argument(s) == file path(s)      2
          FirefoxHistory         - Parses any found FireFox history files      2
          FirefoxPresence        - Checks if interesting Firefox files exist      2
          IdleTime               - Returns the number of seconds since the current user's last input.      2
          IEFavorites            - Internet Explorer favorites      2
          IETabs                 - Open Internet Explorer tabs      3
          IEUrls                 - Internet Explorer typed URLs (last 7 days, argument == last X days)      3
          InstalledProducts      - Installed products via the registry      3
          InterestingFiles       - "Interesting" files matching various patterns in the user's folder. Note: takes non-trivial time.      3
        + InterestingProcesses   - "Interesting" processes - defensive products and admin tools      3
          InternetSettings       - Internet settings including proxy configs      3
        + LAPS                   - LAPS settings, if installed      3
        + LastShutdown           - Returns the DateTime of the last system shutdown (via the registry).      3
          LocalGPOs              - Local Group Policy settings applied to the machine/local users      3
        + LocalGroups            - Non-empty local groups, "-full" displays all groups (argument == computername to enumerate)      3
        + LocalUsers             - Local users, whether they're active/disabled, and pwd last set (argument == computername to enumerate)      4
          LogonEvents            - Logon events (Event ID 4624) from the security event log. Default of 10 days, argument == last X days.      4
        + LogonSessions          - Windows logon sessions      4
        + LSASettings            - LSA settings (including auth packages)      4
        + MappedDrives           - Users' mapped drives (via WMI)      4
          NamedPipes             - Named pipe names and any readable ACL information.      4
        + NetworkProfiles        - Windows network profiles      4
        + NetworkShares          - Network shares exposed by the machine (via WMI)      4
        + NTLMSettings           - NTLM authentication settings      4
          OfficeMRUs             - Office most recently used file list (last 7 days)      4
          OSInfo                 - Basic OS info (i.e. architecture, OS version, etc.)      5
          OutlookDownloads       - List files downloaded by Outlook      5
          PoweredOnEvents        - Reboot and sleep schedule based on the System event log EIDs 1, 12, 13, 42, and 6008. Default of 7 days, argument == last X days.      5
        + PowerShell             - PowerShell versions and security settings      5
          PowerShellEvents       - PowerShell script block logs (4104) with sensitive data.      5
          Printers               - Installed Printers (via WMI)      5
          ProcessCreationEvents  - Process creation logs (4688) with sensitive data.      5
          Processes              - Running processes with file info company names that don't contain 'Microsoft', "-full" enumerates all processes      5
        + ProcessOwners          - Running non-session 0 process list with owners. For remote use.      5
        + PSSessionSettings      - Enumerates PS Session Settings from the registry      5
        + PuttyHostKeys          - Saved Putty SSH host keys      6
        + PuttySessions          - Saved Putty configuration (interesting fields) and SSH host keys      6
          RDCManFiles            - Windows Remote Desktop Connection Manager settings files      6
        + RDPSavedConnections    - Saved RDP connections stored in the registry      6
        + RDPSessions            - Current incoming RDP sessions (argument == computername to enumerate)      6
          RecycleBin             - Items in the Recycle Bin deleted in the last 30 days - only works from a user context!      6
          reg                    - Registry key values (HKLM\Software by default) argument == [Path] [intDepth] [Regex] [boolIgnoreErrors]      6
          RPCMappedEndpoints     - Current RPC endpoints mapped      6
        + SCCM                   - System Center Configuration Manager (SCCM) settings, if applicable      6
        + ScheduledTasks         - Scheduled tasks (via WMI) that aren't authored by 'Microsoft', "-full" dumps all Scheduled tasks      6
          SearchIndex            - Query results from the Windows Search Index, default term of 'passsword'. (argument(s) == <search path> <pattern1,pattern2,...>      7
          SecurityPackages       - Enumerates the security packages currently available using EnumerateSecurityPackagesA()      7
          Services               - Services with file info company names that don't contain 'Microsoft', "-full" dumps all processes      7
          SlackDownloads         - Parses any found 'slack-downloads' files      7
          SlackPresence          - Checks if interesting Slack files exist      7
          SlackWorkspaces        - Parses any found 'slack-workspaces' files      7
        + Sysmon                 - Sysmon configuration from the registry      7
          SysmonEvents           - Sysmon process creation logs (1) with sensitive data.      7
          TcpConnections         - Current TCP connections and their associated processes and services      7
          TokenGroups            - The current token's local and domain groups      7
          TokenPrivileges        - Currently enabled token privileges (e.g. SeDebugPrivilege/etc.)      8
        + UAC                    - UAC system policies via the registry      8
          UdpConnections         - Current UDP connections and associated processes and services      8
          UserRightAssignments   - Configured User Right Assignments (e.g. SeDenyNetworkLogonRight, SeShutdownPrivilege, etc.) argument == computername to enumerate      8
        + WindowsAutoLogon       - Registry autologon information      8
          WindowsCredentialFiles - Windows credential DPAPI blobs      8
        + WindowsDefender        - Windows Defender settings (including exclusion locations)      8
        + WindowsEventForwarding - Windows Event Forwarding (WEF) settings via the registry      8
        + WindowsFirewall        - Non-standard firewall rules, "-full" dumps all (arguments == allow/deny/tcp/udp/in/out/domain/private/public)      8
          WindowsVault           - Credentials saved in the Windows Vault (i.e. logins from Internet Explorer and Edge).      8
          WMIEventConsumer       - Lists WMI Event Consumers      9
          WMIEventFilter         - Lists WMI Event Filters      9
          WMIFilterBinding       - Lists WMI Filter to Consumer Bindings      9
        + WSUS                   - Windows Server Update Services (WSUS) settings, if applicable
```


Seatbelt拥有以下一些命令组：

*   All
*   User
*   System
*   Slack
*   Chrome
*   Remote
*   Misc

不同用户组能执行的模块不一样，具体如下：

Seatbelt.exe -group=all 运行所有的模块

Seatbelt.exe -group=user 运行以下的模块

```
ChromePresence, CloudCredentials, CredEnum, dir, DpapiMasterKeys,      
ExplorerMRUs, ExplorerRunCommands, FirefoxPresence, IdleTime,      
IEFavorites, IETabs, IEUrls, MappedDrives,      
OfficeMRUs, PuttyHostKeys, PuttySessions, RDCManFiles,      
RDPSavedConnections, SlackDownloads, SlackPresence, SlackWorkspaces,      
TokenGroups, WindowsCredentialFiles, WindowsVault
```


Seatbelt.exe -group=system 运行以下的模块

```
AMSIProviders, AntiVirus, AppLocker, ARPTable, AuditPolicies,      
AuditPolicyRegistry, AutoRuns, CredGuard, DNSCache,      
DotNet, EnvironmentPath, EnvironmentVariables, InterestingProcesses,      
InternetSettings, LAPS, LastShutdown, LocalGPOs,      
LocalGroups, LocalUsers, LogonSessions, LSASettings,      
NamedPipes, NetworkProfiles, NetworkShares, NTLMSettings,      
OSInfo, PoweredOnEvents, PowerShell, Printers,      
Processes, PSSessionSettings, RDPSessions, SCCM,      
Services, Sysmon, TcpConnections, TokenPrivileges,      1
UAC, UdpConnections, UserRightAssignments, WindowsAutoLogon,      1
WindowsDefender, WindowsEventForwarding, WindowsFirewall, WMIEventConsumer,      1
WMIEventFilter, WMIFilterBinding, WSUS
```


Seatbelt.exe -group=slack 运行以下的模块

```
SlackDownloads, SlackPresence, SlackWorkspaces
```


 Seatbelt.exe -group=chrome 运行以下的模块

```
ChromeBookmarks, ChromeHistory, ChromePresence
```


Seatbelt.exe -group=remote  运行以下的模块

```
AMSIProviders, AntiVirus, DotNet, ExplorerRunCommands, InterestingProcesses,      
LastShutdown, LogonSessions, LSASettings, MappedDrives,      
NetworkProfiles, NetworkShares, NTLMSettings, PowerShell,      
ProcessOwners, PuttyHostKeys, PuttySessions, RDPSavedConnections,      
RDPSessions, Sysmon, WindowsDefender, WindowsEventForwarding,      
WindowsFirewall
```


Seatbelt.exe -group=misc 运行以下的模块

```
ChromeBookmarks, ChromeHistory, ExplicitLogonEvents, FileInfo, FirefoxHistory,      
InstalledProducts, InterestingFiles, LogonEvents, OutlookDownloads,      
PowerShellEvents, ProcessCreationEvents, ProcessOwners, RecycleBin,      
reg, RPCMappedEndpoints, ScheduledTasks, SearchIndex,      
SecurityPackages, SysmonEvents
```


注意：只有提升到管理员权限之后才能执行所有的模块，普通权限执行的模块有限。

### 运行所有检查

```
#以下命令将运行所有检查并返回所有输出      
Seatbelt.exe -group=all -full
```


![](https://img-blog.csdnimg.cn/20200807144203225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

也可以将运行结果输出到文件中

```
Seatbelt.exe -group=all -full > test.txt
```


![](https://img-blog.csdnimg.cn/20200807144618123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 检查系统相关

```
#运行检查系统相关的信息      
Seatbelt.exe -group=system
```


###  检查用户相关

```
运行检查以挖掘有关当前登录用户（如果未提升）或ALL用户（如果提升）的有趣数据。      
Seatbelt.exe -group=user
```


### 杂项 

```
#运行所有其他检查。      
Seatbelt.exe -group=misc
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

项目地址：[https://github.com/GhostPack/Seatbelt](https://github.com/GhostPack/Seatbelt "https://github.com/GhostPack/Seatbelt")