# Tun-er-Up
> [!CAUTION]
> In order of performance scaling, Hardware > BIOS > Operating System.

> [!CAUTION]
> **Do NOT** blindly trust or believe everything you read online (including this resource) and typically doubt everything. Instead, validate statements through evidence, research and benchmarks.

> [!CAUTION]
> **Do NOT** apply random, unknown or undocumented changes, programs and script to your system without a comprehensive understanding of what they are changing and impact they have on security, privacy and performance.

At the moment we are going to add a couple things, and as of now we are just going to be doing Windows tuning

<h1 id="physical-setup"> Physical Setup <a href="#physical-setup">(permalink)</a></h1>

<h1 id="biosuefi"> BIOS/UEFI <a href="#biosuefi">(permalink)</a></h1>

<h1 id="iso-creation"> ISO Creation <a href="#iso-creation">(permalink)</a></h1>

- See [docs/iso-creation.md](/docs/iso-creation.md)

<h1 id="configure-windows"> Configure Windows <a href="#configure-windows">(permalink)</a></h1>

<h2 id="oobe-setup"> OOBE Setup <a href="#oobe-setup">(permalink)</a></h2>

- Windows Server may force you to enter a password which can be optionally be removed in later steps

- If you are configuring Windows 11, press ``Shift+F10`` to open CMD, then type ``regedit`` to open the registy editor to add the registry entry below. This will allow us to continue without an internet connection by unlocking the ``continue with limited setup`` option as demonstrated in the video examples below. This removes the requirement to sign in with a Microsoft account which I highly advise against for privacy reasons generally speaking. After the registry entry has been applied, type ``shutdown /r /t 0`` in CMD to restart.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\OOBE]
    "BypassNRO"=dword:00000001
    ```
    
- Second Option, press ``Shift+F10`` to open CMD after you see the "Let's connect you to a network" screen, then type ``start ms-cxh:localonly``([1](https://massgrave.dev/clean_install_windows#bypass-windows-11-internet-and-microsoft-account-requirements)).

<h2 id="file-system">11.2. File System <a href="#file-system">(permalink)</a></h2>

Open CMD as administrator and enter the commands below.

- Disable the creation of 8.3 character-length file names on FAT and NTFS-formatted volumes which aids performance and security ([1](https://web.archive.org/web/20200217151754/https://ttcshelbyville.wordpress.com/2018/12/02/should-you-disable-8dot3-for-performance-and-security))

  - Disable the creation of 8.3 character-length file names

    ```bat
    fsutil 8dot3name set 1
    ```

  - If the steps carried out in section [Booting Into the ISO](https://github.com/randomdukfuk/Tun-er-Up/blob/main/docs/iso-creation.md#booting-into-the-iso) to strip 8dot3 names was followed correctly, the command below should display a value close to 0 for "total 8dot3 names found"

    ```bat
    fsutil 8dot3name scan /s C:
    ```

- Disable updates to the Last Access Time stamp on each directory when directories are listed on an NTFS volume. Disabling the Last Access Time feature improves the speed of file and directory access ([1](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-behavior#remarks)). Beware that this may affect backup and remote storage programs as per the official remarks ([1](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fsutil-behavior#remarks))

    ```bat
    fsutil behavior set disablelastaccess 1
    ```

<h2 id="user-account-control">11.3. User Account Control <a href="#user-account-control">(permalink)</a></h2>

Set the User Account Control (UAC) setting to "Always notify" (highest level) by typing ``useraccountcontrolsettings`` in ``Win+R``. This reduces the risk of a malicious program bypassing UAC, which can occur with the default setting ([1](https://devblogs.microsoft.com/oldnewthing/20160816-00/?p=94105), [2](https://github.com/hfiref0x/UACME#system-requirements)).

<h2 id="unrestricted-powershell-execution-policy"> Unrestricted PowerShell Execution Policy <a href="#unrestricted-powershell-execution-policy">(permalink)</a></h2>

> [!WARNING]
> 🔒 Setting the PowerShell Execution Policy to Unrestricted may negatively impact security and expose the system to vulnerabilities. Users should evaluate the security risks associated with modifying the specified setting. Alternatively, ``-ExecutionPolicy Bypass`` can be used when starting a PowerShell instance instead of configuring it globally.

This is required to execute the scripts within the repository. Open PowerShell as administrator and enter the command below.

```powershell
Set-ExecutionPolicy Unrestricted
```

<h2 id="importing-bin-folder">11.5. Importing bin Folder <a href="#importing-bin-folder">(permalink)</a></h2>

Move the ``bin`` folder that you downloaded prior to installing Windows to the ``C:`` drive as outlined in section [Fetching Required Files](#fetching-required-files). If you haven't downloaded it yet, you will need to fetch it from another system as you don't have network access at this stage. The complete path should be ``C:\bin``.

<h2 id="process-mitigations-windows-10-1709">11.6. Process Mitigations (Windows 10 1709+) <a href="#process-mitigations-windows-10-1709">(permalink)</a></h2>

> [!WARNING]
> 🔒 Disabling process mitigations may negatively impact security and expose the system to vulnerabilities. Users should evaluate the security risks associated with modifying the specified setting.

> [!CAUTION]
> 📊 **Do NOT** blindly follow the recommendations in this section. **Do** benchmark the specified changes to ensure they result in positive performance scaling, as every system behaves differently and changes could unintentionally degrade performance ([instructions](#benchmarking)).

There are several OS-level mitigations ([1](https://learn.microsoft.com/en-us/powershell/module/processmitigations/set-processmitigation?view=windowsserver2019-ps#-disable)) that are enabled by default and may impact performance. If desired, these can be disabled in Windows Defender's "Exploit Protection" page. It should be apparent that disabling mitigations reduces security. This step is carried out now as if you choose to disable Windows Defender in the next steps, the interface will no longer be accessible however they can be toggled using the [Get-ProcessMitigation](https://learn.microsoft.com/en-us/powershell/module/processmitigations/get-processmitigation?view=windowsserver2022-ps) and [Set-ProcessMitigation](https://learn.microsoft.com/en-us/powershell/module/processmitigations/set-processmitigation?view=windowsserver2019-ps) commands in PowerShell. Some programs may require mitigations to be enabled and will break if they are disabled so proceed with caution.

<h2 id="gdid"> GDID <a href="#gdid">(permalink)</a></h2>

Could do this installing windows with no internet connection
([1](https://github.com/SmtimesIWndr/gdid-reversal)), ([2](https://github.com/SmtimesIWndr/We-running-GDID-back))
([script to disable](https://github.com/SmtimesIWndr/GDID-Disabler))


<h2 id="ctmon"> CTFMON.EXE <a href="#ctfmon">(permalink)</a></h2>

There is a process that wasted cpu cycles. Read ([here](https://ctfmon.vercel.app)) for more information.
Using the command below fixes that issue.

```bat
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Input" /v "InputServiceEnabled" /t REG_DWORD /d "0" /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Input" /v "InputServiceEnabledForCCI" /t REG_DWORD /d "0" /f
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
