# Tun-er-Up
> [!CAUTION]
> In order of performance scaling, Hardware > BIOS > Operating System.

> [!CAUTION]
> **Do NOT** blindly trust or believe everything you read online (including this resource) and typically doubt everything. Instead, validate statements through evidence, research and benchmarks.

> [!CAUTION]
> **Do NOT** apply random, unknown or undocumented changes, programs and script to your system without a comprehensive understanding of what they are changing and impact they have on security, privacy and performance.

At the moment we are going to add a couple things, and as of now we are just going to be doing Windows tuning

# Disable Unnecessary Background Activity

## Event Trace Sessions (ETS)

> [!WARNING]
> If you wish to keep Windows Event Logging enabled for reliability purposes (as it can help with diagnosing issues with applications or the operating system), skip this step and ensure you didn't disable the `EventLog` service.
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
