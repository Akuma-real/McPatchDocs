---
title: 一键启动
---
一键启动可以让 McPatchClient 在 Minecraft 启动时自动弹出更新，无需手动运行，适合线上环境使用

在配置一键启动之前，必须要确保 McPatchClient 双击启动没问题！

目前共有三种不同的启动方式，各自有不同的特点，下面是功能对比

| 启动方式                      | 支持游戏版本 | 支持的平台 | 猫反兼容 |
| ----------------------------- | ------------ | ---------- | -------- |
| [Windows平台](#Windows)       | 全版本       | 仅电脑端   | 坏       |
| [Android平台](#Android)       | 全版本       | 仅手机端   | 坏       |
| [模组方式启动](#模组方式启动) | 部分版本     | 全平台     | 好       |

如果你有安装猫反作弊，而 Windows 平台的方法又不能正常工作，可以尝试使用[模组方式启动](#模组方式启动)来避免这个问题。但此方式并非支持所有的Minecraft版本和模组框架

## Windows

1. 首先确保McPatchClient.jar双击启动没问题
2. 将McPatchClient.jar文件移动到`.minecraft`目录里面（注意是里面不是旁边）
3. 打开启动器，找到JVM（Java虚拟机）参数。在现有内容的最前面插入一段`-javaagent:xxx.jar `（`xxx`换成McPatchClient.jar的实际的文件名，注意`.jar`的后面还跟着一个空格别漏了）
4. 启动游戏测试效果

如果游戏无法启动，并且提示找不到 McPatchClient.jar 的文件名，而你又十分确定文件名没有写错时。记得看看你是不是开启了版本隔离，如果是，请将 McPatchClient 的文件移动到 Minecraft 游戏版本的核心文件旁边（核心文件通常由两个同名的jar和json文件组成，在`.minecraft/version/your-version`目录下）

如果配置之后 McPatchClient 并没有随 Minecraft 启动（游戏正常启动也没有闪退啥的），请检查是否是开启了启动器的`版本特定设置`导致配置实际并未生效

---

McPatch 客户端支持通过hmcl的下载整合包功能在线安装，点击[这里](../advance/spell-start.md)来阅读详细教程

### 游戏崩溃

当 McPatchClient 在运行过程中遇到网络问题，或者其它错误时，***会主动崩溃掉 Minecraft 进程！*** 这是刻意的设计

如果启动过程中发生闪退或者崩溃，请首先***翻阅日志末尾***，判断是否是 McPatchClient 主动使得 Minecraft 进程崩溃的，或者其他原因所导致

如果排查日志后发现确实是 McPatchClient 主动崩溃所致，错误信息中会有中文文字很清晰地说明具体是什么原因导致的游戏崩溃。并且每条日志前面都会有`McPatchClient`的字样标明这是一条 McPatchClient 的日志

当然，在 McPatchClient 主动崩溃 Minecraft 进程之前，会有非常显眼的错误提示框告诉你发生什么错误，错误可能是什么原因导致的。当你点击确定或者取消按钮以后，表明你已经知晓了是 McPatchClient 报告的错误之后，McPatchClient 才会真正崩溃掉 Minecraft 进程。

如果你不喜欢这种直接崩溃的做法，可以在配置文件里设置`no-throwing`选项来让McPatchClient遇到错误时不打断游戏启动的过程，而不是弹出崩溃Minecraft询问框。但这样做可能会导致一些莫名其妙的问题（比如有模组未更新就强行进入游戏会导致无法进服）

## Android

教程这里以澪和HMCLPE作为示例

1. 首先确保 McPatchClient 在电脑上双击启动没问题
2. 将 McPatchClient.jar 和配置文件一起复制到游戏目录下，所谓游戏目录就是指 .minecraft 目录，无论何时，McPatchClient.jar 和 config.yml 都需要放到 .minecraft 里面，注意是里面不是旁边
   1. 澪的默认路径：`/sdcard/MioLauncher/.minecraft`
   2. HMCLPE的默认路径：`/sdcard/HMCLPE/.minecraft`
   3. 如果你开启了版本隔离，请将 McPatchClient 的文件移动到 Minecraft 游戏版本的核心文件旁边（核心文件通常由两个同名的 jar 和 json 文件组成，在`.minecraft/version/your-version`目录下）
3. 配置启动参数
   1. 澪：切换到`游戏配置`页面，在游戏参数（JVM参数）的最前面插入一段内容`-javaagent:xxx.jar `（`xxx`换成将 McPatchClient.jar 的实际的文件名，注意`.jar`的后面还跟着一个空格别漏了）接着点击保存按钮，然后重启澪
   2. HMCLPE：切换到`版本列表`，修改全局游戏设置或者特点版本设置，在Java虚拟机参数的最前面插入一段内容`-javaagent:xxx.jar `（`xxx`换成将 McPatchClient.jar 的实际的文件名，注意`.jar`的后面还跟着一个空格别漏了），然后点击房子按钮回到主界面
4. 启动游戏测试效果
   1. 澪：请打开日志窗口观察 McPatchClient 是否运行成功
   2. HMCLPE：截止到撰写教程时未能成功打开日志窗口，只能手动查看 HMCLPE 的日志文件`/sdcard/Android/data/com.tungsten.hmclpe/files/debug/boat_latest_log.txt`
5. 如果游戏启动后马上闪退，请翻阅日志末尾判断是否是参数配置不正确或者其它原因
6. 如果日志只有短短几行，且有出现这样的内容：`Error opening zip file or JAR manifest missing : McPatchClient-1.0.1.jar`说明启动参数配置不正确，McPatchClient.jar这个文件找不到，请检查是否放到了.minecraft目录下面（开启版本隔离后需要放到游戏核心文件旁边）
7. 如果每一行日志信息的开头都有`[McPatchClient]`的字样，说明此次崩溃是由 McPatchClient 引起的，这种情况去翻阅常见问题解答就可以解决，如果是其它复杂的情况，请向报告这个问题

### 优化小提示

Android 平台通常使用 ARM 处理器和 LPDDR 内存，无论是处理器功耗还是内存带宽都相当有限，所以请尽量控制每次客户端体积大小。不要给游戏加载体积特别大的模组（尤指大小超过50Mb以上），不仅会导致更新过程变长，也会影响Minecraft游戏的启动速度

### 游戏闪退崩溃

虽然 McPatchClient 可以跑在 Android 平台上，但是却无法像 PC 端那样显示更新进度条窗口，一切的更新过程都是在后台进行的

当McPatchClient在运行过程中遇到网络问题，或者其它错误时，***会主动崩溃掉 Minecraft 进程！*** 这是刻意的设计

如果启动过程中发生闪退或者崩溃，请首先***翻阅日志末尾***，判断是否是 McPatchClient 主动使得 Minecraft 进程崩溃的，或者其他原因所导致

如果排查日志后发现确实是 McPatchClient 主动崩溃所致，错误信息中会有中文文字很清晰地说明具体是什么原因导致的游戏崩溃。并且每条日志前面都会有`McPatchClient`的字样标明这是一条 McPatchClient 的日志

如果崩溃信息里找不到 McPatchClient 相关的字样说明是其它原因导致的崩溃，也就是说崩溃和 McPatchClient 没有直接的关系

如果你不喜欢这种直接崩溃的做法，可以在配置文件里设置`no-throwing`选项来让 McPatchClient 遇到错误时不打断游戏启动的过程，而不是主动崩溃游戏进程。但这样做可能会导致一些莫名其妙的问题（比如有模组未更新就强行进入游戏会导致无法进服）

## 模组方式启动

模组方式启动优点在于对猫反作弊模组友好，不用配置易出错的 Java 虚拟机参数

但是不足是仅支持部分游戏版本和模组框架，还有能更新的文件范围大大受限，模组文件只能更新除 CoreMod、Minecraft 核心文件、Minecraft 资源文件以外的其它文件

使用教程如下：

首先访问 ModClient 仓库的[Releases页面](https://github.com/BalloonUpdate/ModClient/releases)，下载合适版本的 ModClient 模组文件并安装到你的游戏模组目录中，同时请详细阅读 Releases 页面里的下载说明！

> 目前模组形式一键启动支持的游戏版本有限，如果没有你需要的版本，可以尝试使用别的方式启动

接着将 McPatchClient.jar 文件移动到`.minecraft`目录里面。如果开启了版本隔离，就要移动到 Minecraft 游戏版本的核心文件旁边（核心文件通常由两个同名的jar和json文件组成），比如`.minecraft/versions/your-version/`这里

将 McPatchClient.jar 的文件名后面增加一串文字`-JarClient`（注意大小写），比如`McPatchClient-1.0.5.jar`变成`McPatchClient-1.0.5-JarClient.jar`

> 虽然 ModClient 是为 JarClient 设计，但只要在文件名里加上`-JarClient`，McPatch也能正常运行

接下来就是启动游戏测试效果（如果之前有配置过 javaagent 一键启动请删掉Java虚拟机参数避免重复启动）

---

如果你某些模组文件更新失败，删除失败，但客户端程序日志里又没有明显报错消息，那么你多半是遇到了模组启动优先级的问题。也就是这些更新失败的模组先于 BalloonUpdateModLoader 模组启动了

遇到这个问题尝试在 BalloonUpdateModLoade 模组的文件名最前面加一个英文感叹号`!`来提升 BalloonUpdateModLoader 模组的启动优先级，确保 BalloonUpdateModLoader 先于要被更新的模组启动

---

ModClient 支持给 McPatchClient.jar 本身做自更新，可以点击[这里](../advance/modclient-self-update.md)阅读详细的教程
