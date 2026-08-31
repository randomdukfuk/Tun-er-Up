# Tun-er-Up
> [!CAUTION]
> In order of performance scaling, Hardware > BIOS > Operating System.

> [!CAUTION]
> **Do NOT** blindly trust or believe everything you read online (including this resource) and typically doubt everything. Instead, validate statements through evidence, research and benchmarks.

> [!CAUTION]
> **Do NOT** apply random, unknown or undocumented changes, programs and script to your system without a comprehensive understanding of what they are changing and impact they have on security, privacy and performance.

At the moment we are going to add a couple things, and as of now we are just going to be doing Windows tuning

<h1 id="configure-windows"> Configure Windows <a href="#configure-windows">(permalink)</a></h1>

<h2 id="oobe-setup"> OOBE Setup <a href="#oobe-setup">(permalink)</a></h2>

- Windows Server may force you to enter a password which can be optionally be removed in later steps

- If you are configuring Windows 11, press ``Shift+F10`` to open CMD, then type ``regedit`` to open the registy editor to add the registry entry below. This will allow us to continue without an internet connection by unlocking the ``continue with limited setup`` option as demonstrated in the video examples below. This removes the requirement to sign in with a Microsoft account which I highly advise against for privacy reasons generally speaking. After the registry entry has been applied, type ``shutdown /r /t 0`` in CMD to restart. Second Option is typing ``start ms-cxh:localonly`` after ``Shift+F10`` in CMD when you see the "Let's connect you to a network" screen.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\OOBE]
    "BypassNRO"=dword:00000001
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
