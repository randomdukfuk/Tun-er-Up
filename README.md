# Tun-er-Up
> [!CAUTION]
> In order of performance scaling, Hardware > BIOS > Operating System.

> [!CAUTION]
> **Do NOT** blindly trust or believe everything you read online (including this resource) and typically doubt everything. Instead, validate statements through evidence, research and benchmarks.

> [!CAUTION]
> **Do NOT** apply random, unknown or undocumented changes, programs and script to your system without a comprehensive understanding of what they are changing and impact they have on security, privacy and performance.

At the moment we are going to add a couple things, and as of now we are just going to be doing Windows tuning

# Disable Unnecessary Background Activity

## Drivers and Services

> [!CAUTION]
> This section is targeted towards <ins>**ADVANCED USERS ONLY**</ins>. Improperly following this section may permanently damage your operating system, requiring a reinstall. I am not responsible for any issues that may occur while or due to following this section.
>
> Please familiarize yourself with [service-list-builder](https://github.com/valleyofdoom/service-list-builder) and thoroughly read its entire README before following this section.

> [!WARNING]
> Following this section may negatively impact security as several security features (such as Windows Defender and Firewall) will be disabled.

The main goal of disabling unnecessary services and drivers (from now on referred to as "services") is minimizing unnecessary context switches and CPU cycles wasted by these unused background processes while a real-time application is in use.

The provided config aims to balance resource usage and compatibility. Even so, compatibility issues with many applications may arise while services are disabled, which is why services should be disabled only while a real-time application is in use, and enabled when doing other activities (such as installing or using other applications).

- Windows Defender should be disabled before running service-list-builder as it may interfere with the generated scripts

- The optimal time to generate the scripts is after a clean reinstall of the operating system, before any 3rd-party applications have been installed, as this will allow for 3rd-party services to be installed onto the system later without being disabled by the script

    - If the scripts are generated after 3rd-party applications have been installed, the user-mode services you wish to keep enabled must be added to the `[enabled_services]` section of the config

Copy and paste the following config into the `lists.ini` file in the service-list-builder directory:

```ini
[enabled_services]
Appinfo
AppXSvc
AudioEndpointBuilder
Audiosrv
BrokerInfrastructure
camsvc
CaptureService
CoreMessagingRegistrar
CryptSvc
DcomLaunch
DeviceInstall
DevicesFlowUserSvc
DispBrokerDesktopSvc
Dnscache
EFS
gpsvc
hidserv
KeyIso
LSM
MMCSS
msiserver
netprofm
nsi
PlugPlay
Power
ProfSvc
RpcEptMapper
RpcSs
seclogon
sppsvc
StateRepository
SystemEventsBroker
TextInputManagementService
TrustedInstaller
UserManager
WFDSConMgrSvc
Winmgmt
AMD External Events Utility # Required for: VRR (FreeSync)
Dhcp # Required for: Wi-Fi (set static IP when disabling)
EventLog # Required for: Wi-Fi
Netman # Required for: Wi-Fi
NetSetupSvc # Required for: Wi-Fi
NlaSvc # Required for: Wi-Fi
Wcmsvc # Required for: Wi-Fi
WinHttpAutoProxySvc # Required for: Wi-Fi
WlanSvc # Required for: Wi-Fi
UdkUserSvc # Required for: Windows Start Menu (not required when using alternatives e.g. Open-Shell)
WpnService # Required for: Windows Notifications
WpnUserService # Required for: Windows Notifications
Schedule # Required for: Task Scheduler
TimeBrokerSvc # Required for: Task Scheduler

[individual_disabled_services]
applockerfltr
bfs
EhStorClass
luafv
Ndu
NetBIOS
NetBT
UCPD
UnionFS
WdNisDrv
wtd
ZTDNS
# fvevol # Uncommenting breaks: BitLocker
# msisadrv # Uncommenting breaks: Keyboard on mobile devices
# volsnap # Uncommenting breaks: Win8 and lower
# vwififlt # Uncommenting breaks: Wi-Fi

[rename_binaries]
\Windows\System32\upfc.exe # Uncommenting breaks: Windows Update Auto Repair
\Windows\SystemApps\MicrosoftWindows.Client.CBS_cw5n1h2txyewy\TextInputHost.exe # Uncommenting breaks: Windows Emoji Panel
# \Windows\System32\RuntimeBroker.exe # Uncommenting breaks: Game Bar, Windows Start Menu (not required when using alternatives e.g. Open-Shell)
# \Windows\System32\ctfmon.exe # Uncommenting breaks: Windows Start Menu (not required when using alternatives e.g. Open-Shell)
# \Windows\SystemApps\Microsoft.Windows.StartMenuExperienceHost_cw5n1h2txyewy\StartMenuExperienceHost.exe # Uncommenting breaks: Windows Start Menu (not required when using alternatives e.g. Open-Shell)
# \Windows\System32\ShellHost.exe # Uncommenting breaks: Windows Shell (Internet/Audio button)
```

- Optionally comment/uncomment entries that include a note according to your needs, **carefully read the provided notes when doing so**

- Optionally add unnecessary drivers from [unnecessary-drivers.txt](scripts/unnecessary-drivers.txt) to the `[individual_disabled_services]` section, **please note that this list is not officially supported and may lead to additional compatibility issues**

- If you removed/commented out the `Schedule` and `TimeBrokerSvc` entries, run the following command to prevent the Software Protection service from attempting to schedule a restart every 30 seconds:

    ```cmd
    reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform" /v "InactivityShutdownDelay" /t REG_DWORD /d "4294967295" /f
    ```

- When rebuilding the scripts, make sure to run the generated `Services-Enable.bat` script beforehand as the tool relies on the current state of the registry to generate the scripts

## Event Trace Sessions (ETS)

> [!WARNING]
> If you wish to keep Windows Event Logging enabled for reliability purposes (as it can help with diagnosing issues with applications or the operating system), skip this step and ensure you didn't disable the `EventLog` service in the [Drivers and Services](#drivers-and-services) section.

Event tracing sessions specify which event providers to enable and record events from while they are running. Disabling them helps prevent unnecessary background activity by disabling these providers, which in turn disables Windows Event Logging and makes logging to the Event Log inaccessible to all applications.

Disabling event tracing sessions will break the Windows Event Log service and all services that depend on it. When disabling event tracing sessions, ensure you've [disabled the Windows Search service](https://github.com/valleyofdoom/PC-Tuning#search-indexing), as Windows Explorer will break when this service is enabled but not functioning properly.

Same as with services, ETS should only be disabled while a real-time application is in use, and should be enabled while doing other activities.

Same as with services, the following registry files need to be applied using [NSudo](https://github.com/M2TeamArchived/NSudo/releases) with the `Enable All Privileges` enabled, so I recommend keeping these registry files in the same place as the generated services scripts. The matching registry file should be applied just before running one of the services scripts.

Open CMD as administrator and enter the commands below to build the registry files in the `C:\` directory:

- ``ets-enable.reg``

    ```bat
    reg export "HKLM\SYSTEM\CurrentControlSet\Control\WMI\Autologger" "C:\ets-enable.reg"
    ```

- ``ets-disable.reg``

    ```bat
    >> "C:\ets-disable.reg" echo Windows Registry Editor Version 5.00 && >> "C:\ets-disable.reg" echo. && >> "C:\ets-disable.reg" echo [-HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\WMI\Autologger]
    ```

# Extras
- [valleyofdoom](https://github.com/valleyofdoom/PC-Tuning)
- [Duckleeng](https://github.com/Duckleeng/TweakCollection)
- [Calypto](https://docs.google.com/document/d/1c2-lUJq74wuYK1WrA_bIvgb89dUN0sj8-hO3vqmrau4/edit?tab=t.0)
- [BoringBoredom](https://github.com/BoringBoredom/PC-Optimization-Hub)
- [djdallmann/GamingPCSetup](https://github.com/djdallmann/GamingPCSetup)
