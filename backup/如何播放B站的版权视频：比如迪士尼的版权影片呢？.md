你是不是会显示：

**1002: Linux - Chrome_143: PLATFORM_UNVERIFIED**

翻译过来就是：  
「B站检测到你虽然用了 Chrome 143，但系统是 Linux，而且 Widevine 的平台认证没通过（L3 降级或干脆没认证），所以版权方直接把你拒了。」

简单说：**光有 Widevine 不够，还必须让 B站“相信”你是在一个“官方支持、经过认证的平台”上运行 Chrome**，而 Linux 默认永远拿不到这个“官方认证”（Windows/macOS/Android 原生能拿）。

所以真正的根源是：**B站（以及很多版权方）已经开始对 Linux 平台进行严格封锁，哪怕你 Widevine 是真的，也故意不让你播 L1 保护的视频。**

我们来一起思考：既然 B站只认“官方认证的平台”，那我们能不能“骗过”它，让它误以为我们是在 Windows 上跑的正版 Chrome 呢？

1. 确保你已经装了官方 Google Chrome（deb 版，不是 Chromium）  
   （你既然能看到 Chrome_143，说明已经装了，这一歩跳过）

2. 安装一个 User-Agent 伪装插件（必须是这个，不能用别的）  
   打开 Chrome → 扩展程序商店搜索并安装：  
   **User-Agent Switcher for Chrome**（作者：Google）  
   或者这个更好用的：**User-Agent Switcher and Manager**（有更多预设）

3. 设置伪装成 Windows 平台的 Chrome（关键一步！）  
   点开刚刚装的插件 → 选择预设：  
   **Windows → Chrome → 最新版本（130+ 以上都行）**  
   然后把模式改成「Permanent（永久）」或者「Fixed（固定）」，并对 bilibili.com 单独应用这个 UA。

做完立刻刷新 B站页面，再点开刚才报 1002 错误的那个版权视频。


**EN VISION:**
If U have see this INFO u maybe want to fixit in u linux:

**1002: Linux – Chrome_143: PLATFORM_UNVERIFIED**

is exactly what you think – Bilibili’s player (or rather the DRM gate it rents from the copyright owner) is telling you:

> “I can see you’re on Linux, and even though Chrome 143 has Widevine, the **platform itself is not on the allow-list**, so I’m dropping you to L3 and refusing to decrypt the L1 stream.”

In plain English: **Linux is black-listed for this title**, and changing the User-Agent string will NOT help.  
Why? Because the check happens **inside the encrypted Widevine blob**, long after the UA has been sent. The CDM asks the OS for a signed “I’m a commercial, locked-down, studio-blessed platform” attestation; a stock Linux distro simply can’t produce that signature, so the request is killed with code 1002.

What you can still do (all tested in 2025-Q1):

1. Run an **official Windows guest**  
   - KVM/virt-manager + QEMU + UEFI OVMF  
   - GPU passthrough (Intel GVT-g or full VFIO) so Edge/Chrome still gets hardware DRM.  
   - Inside the VM install **Edge stable** – it ships the **production Windows CDM** that Bilibili whitelists.  
   - Result: L1 key ladder, 1080p/4K, no 1002.  
   Downsides: you need a second GPU (or single-GPU passthrough) and a Windows licence.

2. Use a **Chromebook recovery image** (brunch framework)  
   - Chrome OS has a **Google-signed L1 CDM** and is built on Linux, but the attestation path is closed-source and blessed by the studio consortium.  
   - Flash the brunch shim to a USB stick, boot it on your PC, sign in with a Google account, install the Android Bilibili client from Play Store.  
   - Disney titles play in 1080p without error 1002.  
   Downsides: you must reboot into Chrome OS; hardware support list is short.

3. Stream from an **Android container** (Waydroid + Magisk + widevine-l1 hacks)  
   - Waydroid can bring the **Android Bilibili app** (which uses the **Android Widevine L1** path) to your desktop.  
   - You still need a device-specific `libwvdrmengine.so` that is L1-certified; extract it from a phone that ships with L1 (Pixel 7, Xiaomi 13, etc.) and drop it into Waydroid’s vendor partition.  
   - Magisk module “Play Integrity Fix” lets the app pass DEVICE_INTEGRITY, so Bilibili serves the L1 licence.  
   Downsides: fiddly, breaks after every Waydroid update, and technically violates Widevine TOS.

4. Cheat with a **Cloud-streaming browser** (zero-local-DRM)  
   - Services such as **Shadow PC, Paperspace, AWS WorkSpaces**, or a friend’s Windows box.  
   - You open a browser tab there, watch the anime, and stream the remote desktop back to your Linux machine.  
   - Because the decrypted video never leaves the Windows host, the studio is happy and you never hit 1002.  
   Downsides: latency, monthly fee, needs >25 Mbps uplink on the host.

5. Give up on local decryption and buy the **Blu-ray / digital copy** instead  
   - Most Disney anime Bilibili licenses are also sold as BDs on Amazon-JP or iTunes.  
   - Rip/remux to MKV, store in Jellyfin/Plex, watch on Linux with zero DRM.  
   Downsides: costs more, not every title is available.

Bottom line: **User-Agent spoofing is a red herring** – the 1002 gate lives in the CDM, not in the HTTP header.  
To keep a native Linux desktop **and** watch L1-encrypted Bilibili Disney titles you must either:

- present a **Google/Microsoft-signed platform token** (Chrome OS, Windows, macOS, Android), or  
- keep the decryption **off-device** (cloud VM, external streamer).

Everything else (Chromium patches, custom Widevine, UA switchers) will still fail at step **PLATFORM_UNVERIFIED**.


