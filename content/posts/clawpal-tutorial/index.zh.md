---
title: "ClawPal：让你告别古法养虾 🦞"
date: 2026-02-20
draft: false
isCJKLanguage: true
tags: ["AI", "OpenClaw", "Agent", "ClawPal"]
cover:
  image: 'cover.png'
  alt: 'ClawPal - Desktop Companion for OpenClaw'
  relative: true
---

> 你有没有让你的🦞改过自己的配置？改完发现它把自己改坏了，然后你花了一小时帮它恢复？你是不是也想拥有一个多 Agent 为你打工的 Discord，但是看过很多教程，还是配不好这个虾？

大家新年好呀！这两天过得怎么样，有没有串门的时候带你的 🦞 出来溜溜，跟大家打个招呼？😄

这两天我一个人在横滨过年，一边看春晚一边写代码，『春节 Vibe』 Coding 了属于是哈哈，关键效率还不错！那么我春节 Vibe 了什么呢？这就是今天我要给大家介绍的小工具：ClawPal

这是一个桌面端的 OpenClaw 管理工具，让你可以用可视化界面管理 agents/channels/models/sessions 和其他各种配置，不用再手动编辑 JSON 文件，让你告别古法养🦞！

除了管理本机的实例，ClawPal 还支持用 SSH 连接远程部署的 OpenClaw 并管理，功能跟本地一致。

此外，我还做了一个 Recipes（菜谱）功能，方便我（以及未来的大家）分享自己的最佳实践，让你可以按照引导步骤完成各种有意思的配置，比如给不同 Channel 配置不同的人格（提示词）。

ClawPal 免费开源，支持 macOS、Windows 和 Linux 全平台。

👉 官网下载：[clawpal.zhixian.io](https://clawpal.zhixian.io)

高手已经可以自己下载去玩啦，接下来是手把手教程。

![ClawPal 主界面](img01.jpg)

![ClawPal 功能概览](img02.jpg)

## 为什么做 ClawPal

之前写的两篇文章，一篇讲[多 Agent 协作](https://x.com/zhixianio/status/2021384359245381982)，一篇讲[Discord 的最佳用例](https://x.com/zhixianio/status/2018595121084994002)，大家都很支持，也因此认识了很多好朋友！但实际跟朋友们交流后发现，配置思路虽然有了，实际执行起来还是有点困难。尤其最近 OpenClaw 也不知是不是越来越复杂的原因，自己改配置的能力比以前弱了不少，经常把自己改坏或者改错地方，幻觉严重。

所以我就想，有没有办法既能让日常操作变得更容易、减少配置的不确定性，又能方便我分享一些最佳实践？让大家不用自己配置，也不用把文章丢给 Agent 去配置，而是用一个比 Skill 更轻的方式，就像菜谱一样，无论是红烧🦞还是清蒸🦞，直接就能用起来。

想来想去，干脆做成工具吧！ClawPal 就诞生了。

## 连接你的 OpenClaw（支持 SSH）

ClawPal 界面的最上方是连接栏，有一个默认的 `Local` 选项，如果你本机装了 OpenClaw 就会自动显示在这里；右边有个「+SSH」按钮，点击后可以设置远程 SSH 机器。如果那台机器上也跑了 OpenClaw，你也能像管理本地实例一样管理它。

![连接栏和 SSH 设置](img03.jpg)

这对在独立机器上跑 OpenClaw 的小伙伴特别有用，这样无论你平时用什么样的电脑，都不耽误你远程养殖你的🦞，远程养虾从此变得如此简单！

## Status：一键备份升级和模型切换

这里显示一些 OpenClaw 实例的基本信息，包括健康情况和版本号，有新版本也会在这里提示。另外，ClawPal 提供一站式的先备份后升级的能力，防止意外出现的时候丢失数据。这也是我日常指挥 Agent 干的事：更新前一定先备份，无备份不更新。

![Status 页面](img04.jpg)

所有历史备份都在页面底部的 Backups 区域里。在这里能看到每个备份的内容，可以一键 Restore，不需要的也可以 Delete。另外如果刚备份完没显示，右键 Reload 一下页面就可以了。

![Backups 区域](img05.jpg)

## Chat：随时问问题

每个页面右上角都有个 Chat 按钮，它是直连你的 OpenClaw 的，点开后可以选择你想聊的 Agent（如果你有多个的话）。另外，这里的聊天是一个专用 Session，不会跟其他 Session 混淆。我还加了一些预设提示词，让 Agent 大概知道在 ClawPal 里能做哪些事情，遇到非标问题直接在这问挺方便的。

![Chat 面板](img06.jpg)

## Agent 管理

大家可以看到我的 Agent 列表，其中有独立人格（工作区）的 Agent 是第一级，卡片上有 Emoji 和名称；如果你像我一样在独立 Agent 下建了很多子 Agent（没有独立工作区，但有不同的模型或配置），就会作为第二级显示在下面。

![Agent 列表](img07.jpg)

比如我有个 `Owlia-lite` 用来处理简单工作，还有其他专用 Agent 或为了这次做测试创建的，一目了然。当然，你也可以非常简单地在这里切换不同 Agent 的 model，是不是很方便！

## Recipes：最佳实践手把手

这是我给 ClawPal 设计的比较亮点的功能，也是我做这个工具的主要原因之一。现在的 Recipes 还只有内置的两个，未来会继续扩展：

![Recipes 页面](img08.jpg)

**Create dedicated Agent for Channel**：为某个 Channel 创建一个专用 Agent。给它起名字、选 Model（比如想给某个 Channel 分配轻量级任务，就选轻量级 Model），然后选 Guild 和 Channel。我专门把 Channel 名字解析出来了，大家就不用对着一堆数字 ID 去找了哈哈。

![Create Channel Agent 步骤 1](img09.jpg)

![Create Channel Agent 步骤 2](img10.jpg)

如果不只是设置基础 Agent，而是想让它有独立 Workspace，勾选下面的 Checkbox 就会多出几个选项，可以输入昵称、Emoji 和人格特点。

![独立 Workspace 选项](img11.jpg)

步骤走完后，左下角会出现 Pending Changes。点 Apply 会出现一个 Diff 页面，让你看看这次操作在配置文件里改了什么。没问题就点 Apply and Restart，有问题可以 Cancel 然后 Discard。

![Pending Changes 和 Diff](img12.jpg)

**Channel Persona**：这是之前评论区的朋友给我的指导：OpenClaw 允许你在每个 Channel 里设置类似系统提示词的东西。这样哪怕不是独立 Agent，进这个 Channel 时也会被注入一段提示词，让它知道该调什么工具或用什么人格。这算是一种轻量级的 Channel 分治做法，相比之下上一个 Recipe 就是相对重的做法了。

![Channel Persona](img13.jpg)

Recipes 页面上面还有一个从外部加载的功能，这是我预留的接口。后续等我把 Recipe 系统设计得更好，就可以开放给大家写自己的 Recipe，自己用或者分享都可以。我也会在官网上专门开一个区域，让大家可以一键加载 Recipe。Recipes 目前的设计里没有可执行代码，都是一些配置 Patch，比 Skill 轻很多，也更放心。

## Channels：统一管理你的各种聊天配置

这应该是最常用的功能了。可以快速给每个 Channel 配置 Agent。这个 Agent 不一定是独立人格的，其实就是给它配不同的 Model。至于配不同的 Persona，现在先用 Recipe 功能去做，这里还没直接支持。

![Channels 管理](img14.jpg)

## History：配置变更记录，一键回滚

每次配置操作都会形成一条 History，如果你觉得某次修改有问题，可以点击 Rollback 回滚，不过之前你应该会想先 Preview 一下 Rollback 会把配置改成什么样子。

![History 页面](img15.jpg)

提醒一点：点击 Rollback 不是立即生效的，还需要再点 Apply Changes。所以如果你不小心点了 Rollback 也不用担心，直接左下角 Discard，刚才的 Rollback 就失效了，在 History 还能看到你 Discard 这次 Rollback 的记录。总之在 ClawPal 里的操作流程应该是相对安全的，大家可以放心。

## Doctor：终于有好用的 Session 管理器了！

Doctor 页面现在做得相对简单，并没有真的把很多 Doctor 功能做进来。因为我想来想去，在本机上再做一个 Doctor 意义不大，可能未来需要做一个远端的 Doctor 服务，到时候前端做好操作审计和授权即可。现在 Doctor 里唯一的功能就是 Session 清理。

![Doctor 页面](img16.jpg)

点 Analyze，等几秒就可以看到每个 Agent 下面这些 Session 到底有什么了，有多少被认为没什么价值，有多少被认为有价值。这个价值判断大家只当参考就行，真想删的话，最好还是点右边 Details，列出所有 Session，每个都可以点开看内容。觉得确实没用或者是意外生成的，就勾选掉然后 Delete。

![Session 详情](img17.jpg)

我觉得这个对强迫症来说挺解压的。经常在 Discord 里 autoThread 一打开，可能说不了两句话就多了一个 Session，强迫症就抓心挠肝受不了，以后用 ClawPal 手动清一清感觉就很好了哈哈！

## Settings：管理你的各种 Model & API Key

现在就做一件事：管理你的 AI 服务的各种 Profile，也就是 Provider（比如 OpenAI、Anthropic、Google）和对应的 API Key。

![Settings 页面](img18.jpg)

虽然不支持在左侧直接 Add OAuth，但你仍然可以用 OpenClaw 自带的 OAuth 方式登录，登录完右边会刷出来对应的 Profile，就可以在其他地方配置的时候选用了。

另外，如果你已经加过比如 Anthropic 的 API Key，之后只是想换个 Model（比如默认用了 Opus 想换成 Sonnet），这时候 API Key 那栏就会显示「optional - key already available」，不需要再填一遍了。

---

好了，第一版就先上线这些功能，应该暂时够用了。不过还是要提醒大家，这个工具一共做了不到 3 天，使用的时候可能会遇到 Bug，毕竟不像正式产品有充足测试，还请见谅！我这里还有一些计划中的功能，后续会慢慢推出，也非常欢迎大家到 Discord（[主页](https://clawpal.zhixian.io)上有入口）提 Bug 和需求，大家一起把它做得越来越好用，告别古法养虾！
