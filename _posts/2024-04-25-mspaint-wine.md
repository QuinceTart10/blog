---
author: martin
title: How I managed to run Windows 7 Paint under Wine
description: This post goes over how and why I've ran Microsoft Paint under Wine
motd: we got mspaint on linux before gta 6 woo
---
Yes, I've managed to successfully run Microsoft Paint under Wine. It took me like 2 days, but in my opinion it was worth it.

The only issues I have found are that the color picker is missing, the text tool and writing to the recently opened list crash the program and some brushes don't work (interestingly enough, some work only on shapes, but not as regular brushes). The advanced color picker still works just fine by the way.

## But why?

It's simple: sometimes when I have a Windows 7 machine lying around, I open up Paint and draw something in it just for fun. This is something I'd love to have on Linux too.

I also couldn't find any guides online on how to run Paint under Wine, so I had to experiment. It of course didn't work by just copying the needed files.

## And how?

You are probably asking how I did it then. It's a bit harder than running your average program. (aka installing some DLLs and boom you're done) The reason why it doesn't want to work is because Wine's implementation of UIRibbon and MUI support is insufficient for mspaint.

Thankfully solving it is as simple as resource hacking the MUI files into the EXEs, copying some DLLs and overriding them.

## The guide

### Requirements

For this guide you need:

- a Windows 7 computer
- a Linux computer with Wine installed (I personally tested it with Wine 8.21-staging)
- a connection between these two
- [Resource Hacker](https://www.angusj.com/resourcehacker/)

### Step-by-step

First of all, you need to get all the needed files from the Windows 7 computer. We only need `mfc42u.dll`, `mspaint.exe`, `UIRibbon.dll`, `UIRibbonRes.dll` (all located in `C:\Windows\System32`), `mspaint.exe.mui` and `UIRibbon.dll.mui` (located in `C:\Windows\System32\[YOUR LANG CODE]`). Put them all in one folder on the Linux computer.

Then get the Segoe UI font from `C:\Windows\Fonts`. You're probably going to be presented with a Control Panel view. Just find the Segoe UI font and press Ctrl+C and then Ctrl+V in another folder. If you don't see that view, just copy all files starting with `segoeui`. On the Linux computer, just copy them to the fonts folder in your Wine prefix. (Usually located at `~/.wine/drive_c/windows/Fonts/`)

Now open up Resource Hacker and load `mspaint.exe` in it. Then open up the Action menu at the top of the window press "Add from a Resource file". A file picker pops up. Find your `mspaint.exe.mui` file (Note: you need to change the file types it shows from "Resource files" to "All files" for some weird reason) and open it.

A dialog asking you which resources it should import pops up. Just press "Check/Uncheck All" and press Import. Now choose "Save As" from the File menu and save it to a new folder.

![Screenshot of a Resource Hacker dialog asking which resources it should import](/assets/attachments/Screenshot_20240424_080448.png)

After you're done with that, just redo the steps above with `UIRibbon.dll` and `UIRibbon.dll.mui`. Now you have your very own modified Paint and UIRibbon files.

The last steps are to move `mfc42u.dll` and `UIRibbonRes.dll` to the system32 folder in your Wine prefix (usually located at `~/.wine/drive_c/windows/system32/`), rename `uiribbon.dll` in that folder to `uiribbon.dll.old`, copy your modified `UIRibbon.dll` to system32 and rename it to `uiribbon.dll` (this is actually pretty important as Linux has case-sensitive filenames), then run `winecfg` in your terminal and set the emulated Windows version to Windows 7. Then go to the Libraries tab, open the dropdown, search for `uiribbon`, press Add and then OK at the bottom of the window.

Now you should be able to run your modified `mspaint.exe` without any major issues. If you use the fallback "classic" theme (labeled as no theme in `winecfg`), Paint is going to crash almost immediately, so you have to switch to a different style.

After running Paint, right-click on the Ribbon and click on "Show Quick Access Toolbar below the Ribbon" to show the action buttons.

## Final note

If you have any questions, improvements or anything, don't hesitate to hit me up on [Discord](https://discord.com/users/862644161156218891) or through [email](mailto:me@quincetart10.is-a.dev). I'd really appreciate if somebody could help me fix any of the issues.
