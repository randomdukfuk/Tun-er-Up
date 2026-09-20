<h1 id="install-windows">10. Install Windows <a href="#install-windows">(permalink)</a></h1>

<h2 id="storage-partitions">10.1. Storage Partitions <a href="#storage-partitions">(permalink)</a></h2>

Set up a [multi-boot](https://en.wikipedia.org/wiki/Multi-booting) system to maintain separate environments for work/bloatware and gaming, ensuring the latter one remains free of bloatware. This allows you to keep the gaming partition clean and free of unnecessary software, as discussed in earlier sections. By doing so, you avoid installing bloatware on the same partition where you use real-time applications without sacrificing usability. To achieve this, shrink a volume in Disk Management ([instructions](https://docs.microsoft.com/en-us/windows-server/storage/disk-management/shrink-a-basic-volume)) to create unallocated space for installing the new operating system.

<h2 id="what-version-of-windows-should-you-use">10.2. What Version of Windows Should You Use? <a href="#what-version-of-windows-should-you-use">(permalink)</a></h2>

This section contains important points to consider that have been collected over the years in regard to Windows versions, compatibility and exclusive features.

- Earlier versions of Windows lack anticheat support (due to lack of security updates from end-of-life OSes), driver support (commonly GPU, NIC) and application support in general, so some users are forced to use newer builds. See the table below of the minimum version required to install drivers for given GPUs. Subject to change

    |GPU|Minimum Windows Version|
    |---|---|
    |NVIDIA 10 series and lower|Supported by almost all Windows versions|
    |NVIDIA 16, 20 series|Win7, Win8, Win10 1709+|
    |NVIDIA 30 series|Win7, Win10 1803+|
    |NVIDIA 40 series|Win10 1803+|
    |AMD|Refer to driver support page|

- Windows Server lacks support for a lot of consumer NICs. Workaround tends such as [this](https://github.com/loopback-kr/Intel-I219-V-for-Windows-Server) tend to interfere with anticheats due to invalid signing certificates

- NVIDIA DCH drivers are supported on Windows 10 1803+ ([1](https://nvidia.custhelp.com/app/answers/detail/a_id/4777/~/nvidia-dch%2Fstandard-display-drivers-for-windows-10-faq))

- During media playback exclusively on Windows 10 1709, the [Multimedia Class Scheduler Service](https://learn.microsoft.com/en-us/windows/win32/procthread/multimedia-class-scheduler-service) raises the timer resolution to 0.5ms which limits control regarding the requested resolution

- Windows 10 1809+ is required for Ray Tracing on NVIDIA GPUs

- Microsoft implemented a fixed 10MHz QueryPerformanceFrequency on Windows 10 1809+

- Windows 10 1903+ has an updated scheduler for multi CCX Ryzen CPUs ([1](https://i.redd.it/y8nxtm08um331.png))

- DirectStorage requires Windows 10 1909+ but according to an article, games running on Windows 11 benefit further from new storage stack optimizations ([1](https://devblogs.microsoft.com/directx/directstorage-developer-preview-now-available))

- Windows 10 2004+ is required for [Hardware Accelerated GPU Scheduling](https://devblogs.microsoft.com/directx/hardware-accelerated-gpu-scheduling) which is necessary for DLSS Frame Generation ([1](https://developer.nvidia.com/rtx/streamline/get-started))

- Processes raising the timer resolution on Windows 10 2004+ no longer affect the global timer resolution ([1](https://learn.microsoft.com/en-us/windows/win32/api/timeapi/nf-timeapi-timebeginperiod), [2](https://randomascii.wordpress.com/2020/10/04/windows-timer-resolution-the-great-rule-change)) meaning that it is set on a per-process basis in which, processes that do not explicitly raise the resolution are not guaranteed a higher resolution and are serviced less often. This was further developed on Windows 11 as a higher resolution is not guaranteed to the calling process if its window is minimized or visibly occluded ([1](https://learn.microsoft.com/en-us/windows/win32/api/timeapi/nf-timeapi-timebeginperiod)). Microsoft added the ability to restore the global behavior on Windows Server 2022+ and Windows 11+ with a registry entry ([1](https://randomascii.wordpress.com/2020/10/04/windows-timer-resolution-the-great-rule-change)) meaning that the implementation can not be restored on Windows 10 versions 2004 - 22H2

- Windows 11+ has an updated scheduler for Intel 12th Gen CPUs and above ([1](https://www.anandtech.com/show/16959/intel-innovation-alder-lake-november-4th/3)) but the behavior can be replicated manually with affinity policies on any Windows version as explained in later sections

- Windows 11+ limits the window message rate of background processes ([1](https://blogs.windows.com/windowsdeveloper/2023/05/26/delivering-delightful-performance-for-more-than-one-billion-users-worldwide))

- Windows 11 is a minimum requirement for [Cross Adapter Scan-Out](https://videocardz.com/newz/microsoft-cross-adapter-scan-out-caso-delivers-16-fps-increse-on-laptops-without-dgpu-igpu-mux-switch) ([1](https://devblogs.microsoft.com/directx/optimizing-hybrid-laptop-performance-with-cross-adapter-scan-out-caso))

- AllowTelemetry can be set to 0 on Windows Enterprise, Education, and Server editions ([1](https://gpsearch.azurewebsites.net:/Default.aspx?PolicyID=10937))

<h2 id="downloading-and-preparing-a-stock-windows-iso">10.3. Downloading and Preparing a Stock Windows ISO <a href="#downloading-and-preparing-a-stock-windows-iso">(permalink)</a></h2>

In order to install Windows, an installation media must be created using an ISO file. Upon downloading ISOs, ensure to cross-check the hashes for the file with official sources to verify that it is genuine and not corrupted. Use the command ``certutil -hashfile <file>`` in CMD to obtain the hashes of the file.

Ensure to download an ISO that contains an edition with group policy support as several policies are configured in later steps. Sometimes, you can get ISOs with specific editions missing so be careful. Below is a list of recommended editions.

- Client editions: Professional
- Server editions: Standard (Desktop Experience)

<h2 id="iso-sources">10.4. ISO Sources <a href="#iso-sources">(permalink)</a></h2>

- [massgrave.dev](https://massgrave.dev/genuine-installation-media)
- [os.click](https://os.click)
- [New Download Links](https://docs.google.com/spreadsheets/d/1zTF5uRJKfZ3ziLxAZHh47kF85ja34_OFB5C5bVSPumk)
- [Adguard File List](https://files.rg-adguard.net)
- [Fido](https://github.com/pbatard/Fido)

<h2 id="iso-preparation">10.5. ISO Preparation <a href="#iso-preparation">(permalink)</a></h2>

<details>
<summary>Windows 7</summary>

If you are configuring Windows 7, I recommend using the ``en_windows_7_professional_with_sp1_x64_dvd_u_676939.iso`` ISO ([Adguard hashes](https://files.rg-adguard.net/file/11ad6502-c2aa-261c-8c3f-c81477b21dd2?lang=en-us)). Aditionally, you won't be able to boot into the ISO on modern hardware without integrating necessary drivers and updates which can be accomplished using tools such as [NTLite](https://www.ntlite.com) ([instructions](https://winraid.level1techs.com/t/guide-integration-of-drivers-into-a-win7-11-image/30793)) or DISM in CLI ([instructions](/docs/image-customization.md)), however NTLite is more user-friendly. Typically, only [NVMe](https://winraid.level1techs.com/t/recommended-ahci-raid-and-nvme-drivers/28310) and [USB](https://winraid.level1techs.com/t/usb-3-0-3-1-drivers-original-and-modded/30871) drivers are required to be integrated into the ISO to even be able to boot into it. Ensure to integrate the drivers in Windows Setup as well otherwise you may have storage detection issues and unusable USB input, unless you plan on installing the ISO with DISM as described in section [Booting Into the ISO](#booting-into-the-iso) because it completely bypasses traditional Windows Setup and the ``boot.wim``. To find drivers that are compatible with your device, search for ones that support your device's HWID ([example](/assets/images/device-hwid-example.png)). If you are unable to find a USB driver for your HWID, try to integrate the [generic USB driver](https://forums.mydigitallife.net/threads/usb-3-xhci-driver-stack-for-windows-7.81934) and the ``KB2864202`` update. Below is a table of updates that I recommend integrating into the ISO.

|Knowledge Base (KB) ID|Notes|
|---|---|
|KB4490628|Servicing Stack Update|
|KB4474419|SHA-2 Code Signing Update|
|KB2670838|Platform Update and DirectX 11.1|
|KB2990941|NVMe Support (<https://files.soupcan.tech/KB2990941-NVMe-Hotfix/Windows6.1-KB2990941-x64.msu>)|
|KB3087873|NVMe Support and Language Pack Hotfix|
|KB2864202|KMDF Update (required for USB 3/XHCI driver stack)|
|KB4534314|Easy anticheat Support|
|KB3191566|WMF 5.1 (<https://www.microsoft.com/en-us/download/details.aspx?id=54616>)|

If you are having trouble with Windows Setup when installing with a USB storage device despite integrating drivers and updates into the ``boot.wim``, you can use modern Windows Setup to install your Windows 7 ISO by placing the Windows 7 ``intall.wim`` in the ``sources`` folder of a Windows 10 USB installation media's ``sources`` folder. Ensure that the language of the ISOs match. This method of installing Windows 7 has alleviated issues for other individuals as modern Windows Setup is equipped with the required components to run on modern hardware and offers greater compatibility.
</details>

<details>
<summary>Windows 8.1</summary>

If you are configuring Windows 8.1, I recommend using the ``en_windows_8_1_x64_dvd_2707217.iso`` ISO ([Adguard hashes](https://files.rg-adguard.net/file/406e60db-4275-7bf8-616f-56e88d9e0a4a?lang=en-us)). Additionally, the table below contains a list of updates that I recommend integrating into the ISO which can be accomplished using tools such as [NTLite](https://www.ntlite.com) ([instructions](https://winraid.level1techs.com/t/guide-integration-of-drivers-into-a-win7-11-image/30793)).

|Knowledge Base (KB) ID|Notes|
|---|---|
|KB2919442|Servicing Stack Update|
|KB2999226|Universal C Runtime|
|KB2919355|Cumulative Update|
|KB3191566|WMF 5.1 (<https://www.microsoft.com/en-us/download/details.aspx?id=54616>)|

</details>

<details>
<summary>Windows 10+</summary>

No additional steps are required for Windows 10+ versions. The latest updates can be inegrated but this is not required if you plan on keeping Windows Update enabled once booted in the ISO. Additionally, ISOs build using UUP dump ship with the latest updates assuming that you build the latest version. This can be achieved using [NTLite](https://www.ntlite.com) ([instructions](https://winraid.level1techs.com/t/guide-integration-of-drivers-into-a-win7-11-image/30793)) or DISM in CLI ([instructions](/docs/image-customization.md)), however NTLite is more user-friendly.
</details>

> [!IMPORTANT]
> The presence of OEMs keys can force the installation of specific editions of Windows editions (e.g. Home) which is explained in section [Downloading and Preparing a Stock Windows ISO](#downloading-and-preparing-a-stock-windows-iso). To circumvent this, you can either customize ``EI.cfg`` and ``PID.txt`` ([instructions](https://www.youtube.com/watch?v=R3yM3AV6q-8)) or remove every edition apart from the edition you would like to install using [NTLite](https://www.ntlite.com) or DISM in CLI ([instructions](/docs/image-customization.md)), however NTLite is more user-friendly.

<h2 id="fetching-required-files">10.6. Fetching Required Files <a href="#fetching-required-files">(permalink)</a></h2>

There are primarily two prerequisites before installing Windows. These can be done later if you are willing to fetch them from another system but I would recommend getting them now. Store these somewhere that you can access offline after installing Windows such as a USB storage device as the installation process consists of not being connected to a network in the initial steps.

1. Download your NIC driver as it may not be packaged with Windows and must be installed in order to connect to a network
2. The ``bin`` folder from this repository which can be downloaded [here](https://github.com/valleyofdoom/PC-Tuning/archive/refs/heads/main.zip)

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
