# Tun-er-Up
> [!CAUTION]
> In order of performance scaling, Hardware > BIOS > Operating System.

> [!CAUTION]
> **Do NOT** blindly trust or believe everything you read online (including this resource) and typically doubt everything. Instead, validate statements through evidence, research and benchmarks.

> [!CAUTION]
> **Do NOT** apply random, unknown or undocumented changes, programs and script to your system without a comprehensive understanding of what they are changing and impact they have on security, privacy and performance.

At the moment we are going to add a couple things, and as of now we are just going to be doing Windows tuning

<h2 id="booting-into-the-iso">10.7. Booting Into the ISO <a href="#booting-into-the-iso">(permalink)</a></h2>

This section covers booting into the ISO retrieved and prepared in the previous section. For the next steps, you are required to disconnect the Ethernet cable and not be connected to the internet during the installation process. This will allow us to bypass the forced Microsoft login during OOBE, allowing us to use Windows with a local account along with preventing installation of unwanted updates and drivers. There are two options when it comes to installing Windows, installing using USB storage or using DISM (without USB storage). Either option can be used. If you want to remove your current operating system and wipe the entire drive, then you will have to install using USB storage because the latter requires dual-booting.

<details>
<summary>Option 1 -  Install using USB storage</summary>

- Download [Ventoy](https://github.com/ventoy/Ventoy/releases) and launch ``Ventoy2Disk.exe``. Navigate to the option menu and select the correct partition style and disable Secure Boot support. The current partition style can be determined by typing ``msinfo32`` in ``Win+R``. Finally, select your USB storage and click install

- Move your Windows ISO into the USB storage in File Explorer

- If Secure Boot is enabled, temporarily disable it for the installation process. Boot into Ventoy on your USB in BIOS and select your Windows ISO. Once setup has finished, Secure Boot can be re-enabled if you had temporarily disabled it

- On Windows 11 24H2+ use the previous version of setup ([example](https://schneegans.de/windows/no-8.3/24h2.png))

- On the legacy language and keyboard selection page (not after this page as this won't work otherwise), prevent Windows setup restarting automatically so that 8dot3 names can be stripped properly as explained in the next steps by pressing ``Shift+F10`` to open CMD then type ``setup /NoReboot``. Continue with setup but don't restart at the end

- When installing Windows 8 with a USB, you may be required to enter a key. Use the generic key ``GCRJD-8NW9H-F2CDX-CCM8D-9D6T9`` to bypass this step. This does not activate Windows

- When installing Win11 with a USB, you may encounter system requirement issues. To bypass the checks, press ``Shift+F10`` to open CMD then type ``regedit`` and add the relevant registry keys listed below

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\Setup\LabConfig]
    "BypassTPMCheck"=dword:00000001
    "BypassRAMCheck"=dword:00000001
    "BypassSecureBootCheck"=dword:00000001
    ```

- After the files are copied to the new partition and before restarting, you can prevent the creation and strip existing 8.3 character-length file names on the volume Windows was just installed to which aids performance and security ([1](https://web.archive.org/web/20200217151754/https://ttcshelbyville.wordpress.com/2018/12/02/should-you-disable-8dot3-for-performance-and-security)). This must be done now (before booting) to prevent file access errors as explained [here](https://schneegans.de/windows/no-8.3)

  - Press ``Shift+F10`` to open CMD

  - Determine the drive letter Windows was installed to by typing ``diskpart``, then type ``list volume`` and determine the correct drive letter. It will be a relatively large boot volume. Type ``exit`` to exit diskpart

  - Disable the creation of 8.3 character-length file names. Replace ``<drive letter>`` with the correct drive letter (e.g. ``D:``)

    ```bat
    fsutil.exe 8dot3name set <drive letter> 1
    ```

  - Strip existing 8.3 character-length file names. Replace ``<drive letter>`` with the correct drive letter (e.g. ``D:``)

    ```bat
    fsutil.exe 8dot3name strip /s /f <drive letter>
    ```

  - Type ``wpeutil reboot`` to exit Windows setup and reboot

</details>

<details>
<summary>Option 2 -  Install using DISM Apply-Image (without USB storage)</summary>

- As this method requires specifying an existing partition to apply the ISO to, create a new partition by [shrinking a volume](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/shrink-a-basic-volume) if you haven't already, then assign the newly created unallocated space a drive letter

- Extract the ISO if required then run the command below to apply the image to a given partition. Replace ``<path\to\wim>`` with the path to the ``install.wim`` or ``install.esd`` (which is located in the ``sources`` folder of the extracted ISO) in each command

  - Get all available editions and their corresponding indexes

      ```bat
      DISM /Get-WimInfo /WimFile:<path\to\wim>
      ```

  - Apply the image by replacing ``<index>`` with the index of the desired edition and ``<drive letter>`` with the drive letter you assigned in the previous step for the image to be mounted on (e.g. index ``1`` and drive letter ``D:``)

      ```bat
      DISM /Apply-Image /ImageFile:<path\to\wim> /Index:<index> /ApplyDir:<drive letter>
      ```

- Create the boot entry with the command below. Replace ``<windir>`` with the path to the mounted ``Windows`` directory (e.g. ``D:\Windows``)

    ```bat
    bcdboot <windir>
    ```

- After the files are copied to the new partition and before restarting, you can prevent the creation and strip existing 8.3 character-length file names on the volume Windows was just installed to which aids performance and security ([1](https://web.archive.org/web/20200217151754/https://ttcshelbyville.wordpress.com/2018/12/02/should-you-disable-8dot3-for-performance-and-security)). This must be done now (before booting) to prevent file access errors as explained [here](https://schneegans.de/windows/no-8.3)

  - Disable the creation of 8.3 character-length file names. Replace ``<drive letter>`` with the correct drive letter (e.g. ``D:``). If the command below fails because the creation of 8dot3 names is globally disabled, first use ``fsutil 8dot3name set 2``, execute the command below, and then  disable it globally again with ``fsutil 8dot3name set 1``. This is only necessary if an error is displayed

    ```bat
    fsutil 8dot3name set <drive letter> 1
    ```

  - Strip existing 8.3 character-length file names. Replace ``<drive letter>`` with the correct drive letter (e.g. ``D:``)

    ```bat
    fsutil 8dot3name strip /s /f <drive letter>
    ```

- The installation process will finish after a system restart

</details>

<h1 id="configure-windows"> Configure Windows <a href="#configure-windows">(permalink)</a></h1>

<h2 id="oobe-setup"> OOBE Setup <a href="#oobe-setup">(permalink)</a></h2>

- Windows Server may force you to enter a password which can be optionally be removed in later steps

- If you are configuring Windows 11, press ``Shift+F10`` to open CMD, then type ``regedit`` to open the registy editor to add the registry entry below. This will allow us to continue without an internet connection by unlocking the ``continue with limited setup`` option as demonstrated in the video examples below. This removes the requirement to sign in with a Microsoft account which I highly advise against for privacy reasons generally speaking. After the registry entry has been applied, type ``shutdown /r /t 0`` in CMD to restart.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\OOBE]
    "BypassNRO"=dword:00000001
    ```
    
- Second Option is typing ``start ms-cxh:localonly`` after ``Shift+F10`` in CMD when you see the "Let's connect you to a network" screen ([1](https://massgrave.dev/clean_install_windows#bypass-windows-11-internet-and-microsoft-account-requirements)).

<h2 id="unrestricted-powershell-execution-policy">Unrestricted PowerShell Execution Policy <a href="#unrestricted-powershell-execution-policy">(permalink)</a></h2>

> [!WARNING]
> 🔒 Setting the PowerShell Execution Policy to Unrestricted may negatively impact security and expose the system to vulnerabilities. Users should evaluate the security risks associated with modifying the specified setting. Alternatively, ``-ExecutionPolicy Bypass`` can be used when starting a PowerShell instance instead of configuring it globally.

This is required to execute the scripts within the repository. Open PowerShell as administrator and enter the command below.

```powershell
Set-ExecutionPolicy Unrestricted
```

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
