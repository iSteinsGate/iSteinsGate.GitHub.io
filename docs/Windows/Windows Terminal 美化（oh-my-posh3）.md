更新 2022/6/20

新方法如下

1. 直接在微软商店搜索 oh my posh 下载（同时若没有 Windows Terminal 的可以安装一下 Windows Terminal）

![](https://pic3.zhimg.com/v2-5f181442b398da011baffc9a4459032a_r.jpg)

![](https://pic4.zhimg.com/v2-efe9daed4c66b89cdaaaf19c210f741b_r.jpg)

2. 下载作者推荐 MesloLGM NF 字体，[点此下载](https://link.zhihu.com/?target=https%3A//github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Meslo.zip)

下载完成后解压，全选右键点击安装即可自动安装

3. 安装完成后打开 Windows Terminal 然后按 "Ctrl+Shift+,（逗号）" 来打开 settings.json 配置文件

将此处

![](https://pic4.zhimg.com/v2-39dc0e9885a5e9ce0b0caceac27aa5af_r.jpg)

改成

![](https://pic4.zhimg.com/v2-ff2f86a84d069d323730256be0f9a9cf_r.jpg)

代码如下

```
"font":
{
    "face": "MesloLGM NF"
}

```

记得保存，然后在 Windows Terminal 中的 powershell 中输入并回车

```
notepad $profile

```

第一次会显示找不到该文件，选择创建新文件，然后输入并保存

```
oh-my-posh init pwsh | Invoke-Expression

```

![](https://pic3.zhimg.com/v2-bfee0b7f0574f29f4d3057302ecd359e_r.jpg)

然后回到 Windows terminal 新建一个 powershell，即可看到以下内容

![](https://pic1.zhimg.com/v2-9c5287d88001ddbcd30a12206adc4cbc_r.jpg)

此处使用的是默认主题，若想修改主题，可以先使用以下命令

```
Get-PoshThemes

```

查看主题，然后在最下面可以找到主题文件的目录

![](https://pic2.zhimg.com/v2-e634df8aee07843cae69bea67001b53d_r.jpg)

按住 Ctrl 点击该链接即可打开该文件夹，然后将所想要的主题的主题文件（一般都是 "主题. omp.json"）路径复制下来，再到 Windows Terminal 中输入

```
notepad $profile

```

打开之前的配置文件

将文件路径加在此处（注意，前面要加上 --config，后面文件路径要去除双引号）此处以 star 主题为例

![](https://pic1.zhimg.com/v2-f0efe80d00c4c42103e3de044adba56c_r.jpg)

然后保存并在 Windows Terminal 中新建一个 powershell 即可看到新主题已成功配置

![](https://pic2.zhimg.com/v2-ddb320be59ff0a548df415e273b92ccd_r.jpg)

新教程至此结束。

以下为原教程

前一段时间想美化一下 Windows Terminal，无奈网上教程比较乱，美化中途也遇到了许多问题，于是，便想写一篇文章关于怎么美化 Windows Terminal。

网上看到的教程大多数是关于 oh-my-posh2 的，现在 oh-my-posh 升级到 3 了，一些地方有些小的变化。关于 oh-my-posh2 的教程，[oh-my-posh 的作者](https://link.zhihu.com/?target=https%3A//github.com/JanDeDobbeleer)在 GitHub 已经讲的很清楚了。

[JanDeDobbeleer/oh-my-posh2](https://link.zhihu.com/?target=https%3A//github.com/JanDeDobbeleer/oh-my-posh2)

## 一. 安装主题 [[1]](#ref_1)

**1. 首先**，先贴上 oh-my-posh 的官方文档：

[Home | Oh my Posh](https://link.zhihu.com/?target=https%3A//ohmyposh.dev/)

**2. 然后**，想必各位都已经事先安装好了 Windows Terminal，没安装好的到 Microsoft store 搜索 “Windows Terminal” 安装即可。

**3.** 安装好之后，**使用管理员身份**打开 Windows Terminal。安装 oh-my-posh 和 posh-git。

*   第一条命令（绕过 power shell 执行策略，使其可以执行脚本文件 <后面会用到>）

```
Set-ExecutionPolicy Bypass

```

*   第二条命令（oh-my-posh 提供主题）

```
Install-Module oh-my-posh -Scope CurrentUser

```

*   第三条命令（posh-git 将 git 信息添加到提示中）

```
Install-Module posh-git -Scope CurrentUser

```

**注意：如果中途有询问，直接 Y 就好了。**

## **二. 编辑相应配置文件** [[2]](#ref_2)

**1. 在 Windows Terminal 中敲下下面两行命令**

*   第一条（启动编辑 power shell 配置文件的引擎）

```
if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }

```

*   第二条（使用记事本打开配置文件）

```
notepad $PROFILE

```

**2. 在打开的记事本中写入如下内容**（**_脚本文件_**）**，并保存**

```
Import-Module posh-git
Import-Module oh-my-posh
Set-PoshPrompt -Theme JanDeDobbeleer

```

*   第一条命令表示导入 posh-git
*   第二条命令表示导入 oh-my-posh
*   第三条命令表示设置主题为 JanDeDobbeleer

**配置完后**，每次打开 Windows Terminal 中的 Power shell 都会执行脚本文件中的三条命令。

```
               #注意：此处的第三行是oh-my-posh2与3的不同之一，在oh-my-posh2里的是：
                                        Set-Theme XXXX
            #如果不是很喜欢这个主题，可以使用以下命令来查看所有主题以及主题的名称.omp
                                        Get-PoshThemes
            #找到喜欢的主题后，可以在之前的脚本文件中将主题名称替换为你想要主题的名称。
                            #注意：此处获取主题在oh-my-posh2的命令为：
                                          Get-Theme

```

这时，你会发现出现了一些方框，效果并不像图片上那么好。那是因为，还没有给主题安装适应的字体。

![](https://pic4.zhimg.com/v2-6744a2687f6245fcf3f7dd5b708228c7_r.jpg)

## **三. 安装 Nerd Fonts 字体并应用** [[3]](#ref_3)

**1. 安装字体**

[Nerd Fonts - Iconic font aggregator, glyphs/icons collection, & fonts patcher](https://link.zhihu.com/?target=https%3A//www.nerdfonts.com/)

*   进入上面的[网站](https://link.zhihu.com/?target=https%3A//www.nerdfonts.com/)
*   点击 **_Downloads_**
*   随便下载一款字体（但个人推荐 <**DejaVuSansMono Nerd Font**> 或 <**Cousine Nerd Font**>，这两套字体比较全，适配也还不错。）
*   下载完成后，**解压**到当前文件夹，然后 CTRL+A 全选，右键点击**安装**，等待安装完成即可。

**2. 使用字体**

*   打开 power shell，并在上方**标签栏**点击下拉按钮找到**设置**，并点击，然后在左侧最下方点击打开 JSON 文件。
*   如果有 **vscode**，将会在 **vscode** 中打开 **settings.json**，这个就是 **Windows Terminal** 的配置文件。
*   这个配置文件最开始几行表示的是**架构**和**默认配置**。下面几行有 3 个包含着字典的列表，分别表示**快捷键（keybindings）、配置（profiles）、配色方案（schemes）**。而我们需要设置的地方在**配置（profiles）**中，在 **profiles** 中，我们能看到有多个字典，我们需要设置美化 power shell，故找到字典中包含：

```
"guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}"

```

这是 power shell 的全局唯一标识符（guid）。

*   找到后，将其中键为 "fontFace" 的键值对改为（如果没有 fontFace 就自己添加一下，放在 guid 下一行，记得加逗号）：

```
"fontFace": "DejaVuSansMono Nerd Font"

```

注意：此处字体的名称请参考上方 [Nerd Fonts 网站](https://link.zhihu.com/?target=https%3A//www.nerdfonts.com/font-downloads)中的字体名称，否则无法显示出来。

*   设置好之后保存 settings.json 文件（若 vscode 未开启自动保存设置，可使用 CTRL+S 进行保存。）
*   完成之后重启 Windows Terminal 即可发现样式改变了，若未改变，请重启 Windows Terminal。

![](https://pic3.zhimg.com/v2-bb147db11591117a813cfce658d0027e_r.jpg)

## 四. 在 vscode 中 power shell 样式（可选）

**1. 使用 _CTRL+，_ 是的，你没有看错 CTRL + 逗号打开 vscode 的设置**

**2. 在顶部输入框输入以下字符：**

```
Integrated:Font Family

```

**3. 在所显示（Terminal › Integrated:Font Family）的输入框中输入（在我的电脑上 Cousine Nerd Font 适配比较好，不会出现偏移的现象）：**

```
Cousine Nerd Font

```

**或**

```
DejaVuSansMono Nerd Font

```

**4. 使用 _CTRL+`_ 召唤终端，即可看到样式发生改变，如果看不到，请重启 vscode。**

![](https://pic1.zhimg.com/v2-d77e3576b75b92c4156cc4bfa11c96f8_r.jpg)

## 小结

Windows Terminal 的美化到此结束，一顿操作下来，是不是比以前好看了不少？结束前还讲了下 vscode 的美化方法，让编写程序的时候能够获得额外的审美体验，岂不美哉？

## 参考

1.  [^](#ref_1_0) 对 GitHub 上的 oh-my-posh2 安装做了一些修改 [https://github.com/JanDeDobbeleer/oh-my-posh2#installation](https://github.com/JanDeDobbeleer/oh-my-posh2#installation)
2.  [^](#ref_2_0)oh-my-posh2 升级到 3 过程中所发生的变化 [https://ohmyposh.dev/docs/upgrading](https://ohmyposh.dev/docs/upgrading)
3.  [^](#ref_3_0) 在 oh-my-posh2 上，需要安装 powerline 字体。而在 on-my-posh3 上，有一些符号已经不支持了，所以需要安装 Nerd Fonts 字体（字符图标样式号称最全）。