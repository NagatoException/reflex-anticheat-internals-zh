# Reflex Anticheat — Internals

![][reflex-logo-big]

> ## [Download Reflex Anticheat (+ Moonlight) for free][download-reflex]




[//]: # (## ℹ️ About)
## ℹ️ 关于

[//]: # (**Reflex** was a premium server-side anticheat for Minecraft &#40;Bukkit&#41;, which was actively developed from 2016 to 2021. This repository serves as its primary legacy. It hosts the latest available anticheat version, which is downloadable and runnable for free; detailed low-level descriptions of Reflex's checks and techiques that it used to fight cheating and provide user friendliness; "stories" and reasoning on why the project was discontinued; and maybe more.)

[//]: # ()
[//]: # (Everything in this repository is licensed under the [CC BY 4.0 License][license] &#40;see the bottom of this file/page for details&#41;.)

[//]: # ()
[//]: # (Many of the documents in this repository might seem large for most people, but a **TL;DR** version is provided in the beginning of most of them, which don't carry the emotion or some important nuances, but still outline the most important information &#40;summary&#41;.)

**Reflex* 是一个用于 Minecraft (Bukkit) 的高级服务器端反作弊插件，从 2016 年到 2021 年一直在积极开发。本存储库作为其主要遗产。它托管了最新可用的反作弊版本，可免费下载和运行；详细的低级描述了 Reflex 的检查和技术，它用来打击作弊和提供用户友好性；关于为什么项目被停止的“故事”和推理；也许还有更多。

本存储库中的所有内容均在 [CC BY 4.0 许可证][license] 下许可（有关详细信息，请参见本文件/页面底部）。

本存储库中的许多文档对大多数人来说可能看起来很大，但是大多数文档的开头都提供了 **TL;DR** 版本，它们不带有情感或一些重要的细微差别，但仍然概述了最重要的信息（摘要）。



[//]: # (## 📚 Table of Contents)
## 📚 目录

[//]: # ()
[//]: # (1. Maintenance / Story)

[//]: # ()
[//]: # (   1.1. [Why Reflex was discontinued][why-discontinued])

[//]: # ()
[//]: # (   1.2. [The current state of Reflex and the dev team][current-state])

[//]: # ()
[//]: # (2. [Download the latest version of Reflex with all Reflex Moonlight features unlocked for free][download-reflex])

[//]: # ()
[//]: # (3. Technical details)

[//]: # ()
[//]: # (   3.1. [Prerequisites][prerequisites])

[//]: # ()
[//]: # (   3.2. KillAura checks &#40;+ AimBot, AimAssist, TriggerBot, and some more&#41;)

[//]: # (   )
[//]: # (   - 3.2.1. [Description of the `ka.acr` check — Perfectly smooth rotations][check.ka.acr])

[//]: # ()
[//]: # (   - 3.2.2. [Description of the `ka.blc` check — "Lazy Aim" and aiming inconsistencies][check.ka.blc])

[//]: # (  )
[//]: # (   - 3.2.3. [Description of the `ka.ra` check — "To click, or not to click" accuracy][check.ka.ra])

[//]: # (  )
[//]: # (   - 3.2.4. [Description of the `ka.yst` check — Absurdly complicated statistical inference for Aimbot detection][check.ka.yst])

[//]: # ()
[//]: # (   - 3.2.5. [Description of the `ka.ray` check — Try to force players to actually look at their target and click properly][check.ka.ray])

[//]: # (  )
[//]: # (   3.3. Lag accounting)

[//]: # ()
[//]: # (   - 3.3.1. [Description of the `badpcks.spf` check — Abusing MC inventory to detect Ping Spoof / Fake Lag][check.badpcks.spf])

[//]: # (  )
[//]: # (   - 3.3.2. [Reliable player lag detection techniques][lag-accounting])

[//]: # (   )
[//]: # (   - 3.3.3. [Analyze packets instead of events when possible][packets-over-events])

[//]: # ()
[//]: # (   3.4. [Reflex Moonlight MC 1.8.x remote cheat files detection][desc-and-list-of-cheat-files-det])

[//]: # ()
[//]: # (   3.5. [Spoofing health to break ESP and KillAura and even crash hacked clients][spoofing-health])

[//]: # ()
[//]: # (   3.6. [Description of the `badpcks.ign` check — Detect and/or prevent Freecam and Blink][check.badpcks.ign])

[//]: # ()
[//]: # (   3.7. [Detecting ScaffoldWalk and Tower &#40;building&#41; hacks][scaffold])

1. 维护 / 故事

   1.1. [为什么 Reflex 停止更新][why-discontinued]

   1.2. [Reflex 的当前状态和开发团队][current-state]

2. [免费下载 Reflex 的最新版本，其中包含所有 Reflex Moonlight 功能][download-reflex]

3. 技术细节

   3.1. [先决条件][prerequisites]

   3.2. 杀戮光环检查（+ AimBot、AimAssist、TriggerBot 等）

    - 3.2.1. [对 `ka.acr` 检查的描述 — 完美的平滑旋转][check.ka.acr]
    - 3.2.2. [对 `ka.blc` 检查的描述 — “懒惰的瞄准”和瞄准不一致][check.ka.blc]
    - 3.2.3. [对 `ka.ra` 检查的描述 — “点击还是不点击”准确性][check.ka.ra]
    - 3.2.4. [对 `ka.yst` 检查的描述 — 荒谬的复杂统计推断，用于 Aimbot 检测][check.ka.yst]
    - 3.2.5. [对 `ka.ray` 检查的描述 — 尝试强制玩家实际上看着他们的目标并正确点击][check.ka.ray]

   3.3. 延迟计算

    - 3.3.1. [对 `badpcks.spf` 检查的描述 — 滥用 MC 物品栏以检测 Ping Spoof / Fake Lag][check.badpcks.spf]
    - 3.3.2. [可靠的玩家延迟检测技术][lag-accounting]
    - 3.3.3. [尽可能分析数据包而不是事件][packets-over-events]

   3.4. [Reflex Moonlight MC 1.8.x 远程作弊文件检测][desc-and-list-of-cheat-files-det]

   3.5. [Spoof生命值以破坏 ESP 和 KillAura 甚至崩溃黑客的客户端][spoofing-health]

   3.6. [对 `badpcks.ign` 检查的描述 — 检测和/或防止 Freecam 和 Blink][check.badpcks.ign]

   3.7. [检测 ScaffoldWalk 和 Tower 作弊][scaffold]

[//]: # (## 💡 Notes)
## 💡 Notes

[//]: # (Contents disclosed in this repository are, of course, not exhaustive. There are quite many other checks and tricks in Reflex which will stay behind the curtains, at least for now. We may be expanding the list of published materials with new exciting stuff later &#40;or maybe not&#41;. Anyway, we hope that the entire Minecraft community will benefit from this all in some way!)
当然，本存储库中披露的内容并不详尽。 Reflex 中有很多其他检查和技巧将暂时留在幕后。 我们可能会在以后发布的新材料中扩展已发布的材料列表（或者可能不会）。 无论如何，我们希望整个 Minecraft 社区都能以某种方式从中受益！

[//]: # (## 🤝 Contributors)
## 🤝 贡献者

[//]: # (Huge thanks to **[all people who had been helping us make Reflex awesome][contrib]**! As well to all the people who kept messaging us and spamming *"free Reflex when*". We really appreciated your interest in the project!)
非常感谢 **[所有帮助我们使 Reflex 变得很棒的人][contrib]**！ 以及所有一直给我们发消息和垃圾邮件的人 *"free Reflex when*". 我们真的很感谢您对该项目的兴趣！

---

[//]: # (## ⚖️ License)
## ⚖️ 许可证

[//]: # (### IMPORTANT: This repository also hosts some pics, just for the memes, which are used throughout the files. They belong to their respective owners &#40;if any&#41;, no claims made for them by Reflex Development Team. If you have any problems with them, please let us know, and we'll address any issues immediately!)
### 重要：本存储库还托管了一些图片，仅用于梗，这些图片在整个文件中使用。 它们属于各自的所有者（如果有），Reflex 开发团队不对它们提出任何要求。 如果您对它们有任何问题，请告诉我们，我们将立即解决这些问题！

[Reflex Anticheat Internals][reflex-anticheat-internals] © 2023 by Reflex Development Team ([DarksideCode (German)][dev-german], [sinnlosername (Florian)][dev-florian], [StevenKGER (Steven)][dev-steven], and others) is licensed under [CC BY 4.0][license]. To view a copy of this license, visit http://creativecommons.org/licenses/by/4.0/

[license]: http://creativecommons.org/licenses/by/4.0

[reflex-anticheat-internals]: https://github.com/NagatoException/reflex-anticheat-internals

[dev-german]: https://github.com/MeGysssTaa

[dev-florian]: https://github.com/sinnlosername

[dev-steven]: https://github.com/StevenKGER

---

## [🏠 Return to main page][reflex-anticheat-internals]

---









[reflex-logo-big]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/8a35007fca0904771d767a1ad466e1d140a79ba6/assets/Reflex%20Logo%20Big.png

[download-reflex]: https://github.com/MeGysssTaa/reflex-anticheat-internals/releases

[why-discontinued]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/1.1.%20Why%20Reflex%20was%20discontinued.md

[current-state]: https://github.com/NagatoExeption/reflex-anticheat-internals-zh/blob/main/texts/1.2.%20The%20current%20state%20of%20Reflex%20and%20the%20dev%20team.md

[prerequisites]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.1.%20Prerequisites.md

[check.ka.acr]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.2.1.%20Description%20of%20the%20%60ka.acr%60%20check.md

[check.ka.blc]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.2.2.%20Description%20of%20the%20%60ka.blc%60%20check.md

[check.ka.ra]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.2.3.%20Description%20of%20the%20%60ka.ra%60%20check.md

[check.ka.yst]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.2.4.%20Description%20of%20the%20%60ka.yst%60%20check.md

[check.badpcks.spf]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.3.1.%20Description%20of%20the%20%60badpcks.spf%60%20check.md

[lag-accounting]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.3.2.%20Reliable%20player%20lag%20detection.md

[desc-and-list-of-cheat-files-det]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.4.%20Reflex%20Moonlight%20remote%20cheat%20files%20detection.md

[packets-over-events]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.3.3.%20Analyze%20packets%20instead%20of%20events%20when%20possible.md

[spoofing-health]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.5.%20Spoofing%20health.md

[check.badpcks.ign]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.6.%20Description%20of%20the%20%60badpcks.ign%60%20check.md

[check.ka.ray]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.2.5.%20Description%20of%20the%20%60ka.ray%60%20check.md

[contrib]: https://github.com/MeGysssTaa/ReflexIssueTracker/wiki/Contributors-List

[scaffold]: https://github.com/NagatoException/reflex-anticheat-internals-zh/blob/main/texts/3.7.%20Detecting%20ScaffoldWalk%20and%20Tower%20(building)%20hacks.md




