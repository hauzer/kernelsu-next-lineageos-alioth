# <img height="28" alt="xiaomi-logo" src="https://github.com/user-attachments/assets/d6fd8ae6-1dd1-4d9f-be16-9631f4a2028b" /> <img height="28" alt="lineageos-logo" src="https://github.com/user-attachments/assets/892b2613-53ee-4431-adfa-d58d6c610b7a" /> <img height="28" alt="kernelsu-next-logo" src="https://github.com/user-attachments/assets/4629eb5d-6681-4e6e-b3af-0bd2b15be5c0" /> <img height="28" alt="susfs-logo" src="https://github.com/user-attachments/assets/13af3f4e-3905-41f3-84c0-79a066f27b71" /> <br> POCO F3: LineageOS + KernelSU-Next + SUSFS

**_Welcome!_** This is a guide on how to build a LineageOS ROM for POCO F3, with KernelSU-Next and SUSFS built-in!

I'll go over the general approach and cover some hurdles I encountered during the process. There's also some patches included, specifically to make KernelSU-Next and SUSFS work together nicely on a non-GKI kernel.

<p align='center'>
  <img width="30%" alt="kernelsu-next" src="https://github.com/user-attachments/assets/67cf6bdd-8500-4357-af32-6d87559fb52d" />
  <img width="30%" alt="susfs" src="https://github.com/user-attachments/assets/6b5e6902-fd90-419a-8478-aa0637cade96" />
  <img width="30%" alt="su" src="https://github.com/user-attachments/assets/b7e28cd5-2e4c-4287-941a-fecff4ff762f" />
</p>

</br>

</br>

> [!TIP]
> You can try out a [precompiled version](https://github.com/hauzer/poco-f3-lineageos-kernelsu-next-susfs/releases/tag/lineageos-22.2_kernelsu-next-1.1.1_susfs-1.5.2%2Br21) if you're so keen. No guarantees!


## 📝 General Info

I won't delve into bootloader unlocking and general custom ROM flashing too much; there's already good resources on this out there.

If you're new to this, unlocking the bootloader for this phone is notoriously hard. One tool which helped me is [HyperSploit](https://github.com/TheAirBlow/HyperSploit), so you might want to take a look at it!

After that, a good starting point is just to try and install the regular, prebuilt LineageOS ROM. You can follow the official guide [here](https://wiki.lineageos.org/devices/alioth/install/variant1/).


## 🌲 The Build Environment

As per LineageOS official guidelines, I used Ubuntu 24 LTS for my build machine. This is a VirtualBox guest inside of a Windows 10 host.

I allocated 32GB of RAM to the VM, but it wasn't enough. I had to add a 16GB swap file, and enable 16GB of ZRAM. I used a 100GB compiler cache.

Additionally, these may be of help:

* [Run apps on a hardware device](https://developer.android.com/studio/run/device)
* [How to enable insecure guest logons in SMB2 and SMB3](https://learn.microsoft.com/en-us/windows-server/storage/file-server/enable-insecure-guest-logons-smb2-and-smb3)


## <img height="24" alt="lineageos-logo" src="https://github.com/user-attachments/assets/892b2613-53ee-4431-adfa-d58d6c610b7a" /> LineageOS

We mostly rely on the LineageOS build [guide](https://wiki.lineageos.org/devices/alioth/build/variant1/). I recommend going through it completely, building the vanilla LineageOS ROM and installing it. This will help you understand the process and also verify that your phone works as it should.

In any case, the rest of the steps in this guide are done before starting the actual build ([Start the build](https://wiki.lineageos.org/devices/alioth/build/variant1/#start-the-build)). Make sure you have the whole LineageOS build environment set-up, including the kernel and proprietary blobs.

> [!TIP]
> We'll do the patching after we set everything else up. I left notes on how I did it manually, if you're interested!


## <img height="24" alt="kernelsu-next-logo" src="https://github.com/user-attachments/assets/4629eb5d-6681-4e6e-b3af-0bd2b15be5c0" /> KernelSU-Next

KernelSU-Next _does_ support non-GKI kernels, but it's not entirely clear from the docs what the exact steps are to enable this.

Follow the official build [page](https://kernelsu-next.github.io/webpage/pages/installation.html) and set up the driver inside the kernel source tree.

> [!NOTE]
> _**How I did it**_
> 
> Follow the KernelSU page on [integrating with non-GKI kernels](https://kernelsu.org/guide/how-to-integrate-for-non-gki.html#manually-modify-the-kernel-source). Ignore the `kprobes` section and just follow the stuff under `Manually modify the kernel source`, including modyfing the code. You should add `CONFIG_KSU=y` to `<KERNEL_ROOT>/arch/arm64/configs/vendor/xiaomi/alioth.config`.


## <img height="24" alt="susfs-logo" src="https://github.com/user-attachments/assets/13af3f4e-3905-41f3-84c0-79a066f27b71" /> SUSFS

SUSFS has an official [branch](https://gitlab.com/simonpunk/susfs4ksu/-/tree/kernel-4.19) for our exact kernel version, 4.19! Clone that branch anywhere and follow steps 4 and 5 under `- Apply SUSFS patches -`.

> [!NOTE]
> _**How I did it**_
> 
> Unfortunately, the patches in the repo are tailored towards KernelSU, so there's a lot of confusion and failed patching involved. The kernel patch mostly works fine, but the KernelSU patch is problematic. Fixing this isn't too complicated, but it is time-consuming. I went through the patches and just manually merged the changes as needed.


## 🩹 Apply the Patches

TODO


## 🔨 Build!

Continue with the LineageOS build guide at [Start the build](https://wiki.lineageos.org/devices/alioth/build/variant1/).

The build took around 2 to 3 hours on my machine.

If you encounter any errors, unfortunately, there's no particular tips which I can put into this doc. Every error that I encountered was pretty specific, due to manual code modification.


## ⚡ Installation

Using your fresh, newly-built files, continue following the LineageOS [installation](https://wiki.lineageos.org/devices/alioth/install/variant1/#installing-lineage-recovery-using-fastboot) guide. Note that you _don't_ have to do a factory reset if you already have LineageOS installed.

## 🧩 Modules

This is (I think) the bare minimum you should install post-flash (specific tested versions included):

* [KernelSU-Next Manager Spoofed v1.1.1](https://github.com/KernelSU-Next/KernelSU-Next/releases/tag/v1.1.1)
* [ReZygisk v1.0.0-rc.3 Release](https://github.com/PerformanC/ReZygisk/releases/tag/v1.0.0-rc.3)
* [LSPosed 1.9.2 Zygisk](https://github.com/LSPosed/LSPosed/releases/tag/v1.9.2)
* [SUSFS module v1.5.2+ Revision 21](https://github.com/sidex15/susfs4ksu-module/releases/tag/v1.5.2%2B_R21)

## 🚀 _Fin_

That's it! Everything should be working nice and up and running!
