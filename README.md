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
<h2 id="window-message-rate"> Background Window Message Rate (Windows 11 22H2+) <a href="#window-message-rate">(permalink)</a></h2>

Windows 11 22H2+ limits the window message rate of background processes ([1](https://blogs.windows.com/windowsdeveloper/2023/05/26/delivering-delightful-performance-for-more-than-one-billion-users-worldwide)). In addition to the introduction of this feature in the [22621.1928](https://support.microsoft.com/en-gb/topic/june-27-2023-kb5027303-os-build-22621-1928-preview-1ada2c0a-fa85-43f8-91c4-6ee13fdf278b) update, a few registry options were also introduced to control the behaviour of this feature. One option allows adjustment of the message rate for background windows. By default, this interval is roughly 8ms/125Hz (0x8). This can be observed by starting a [Mouse Tester](https://github.com/valleyofdoom/MouseTester) log, clicking on another window to send Mouse Tester to the background, and then moving the mouse. After plotting and cropping the logged data to the period when Mouse Tester was in the background while moving the mouse, the polling interval should be close to the value of ``RawMouseThrottleDuration``. The interval can be increased to further exaggerate its effect.

```
[HKEY_CURRENT_USER\Control Panel\Mouse]
"RawMouseThrottleDuration"=dword:00000008 ; min: 0x3, max: 0x14
```

# Extras
- [valleyofdoom](https://github.com/valleyofdoom/PC-Tuning)
- [Duckleeng](https://github.com/Duckleeng/TweakCollection)
- [Calypto](https://docs.google.com/document/d/1c2-lUJq74wuYK1WrA_bIvgb89dUN0sj8-hO3vqmrau4/edit?tab=t.0)
- [BoringBoredom](https://github.com/BoringBoredom/PC-Optimization-Hub)
- [djdallmann/GamingPCSetup](https://github.com/djdallmann/GamingPCSetup)
