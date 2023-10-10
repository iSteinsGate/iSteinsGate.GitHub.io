导入到 Obsdian 插件升级到 1.2.0，增加了自动导入标注到 Obsidian 的功能，此文为一站式配置教程，查看此插件的文档说明，请 [前往这里](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/discussions/2902)。

## 视频

## 特点

不使用 [同步助手](https://link.zhihu.com/?target=http%3A//ksria.com/simpread/docs/%23/Sync%3Fid%3D%25e5%25af%25bc%25e5%2587%25ba%25e6%259c%258d%25e5%258a%25a1) 方案，即可将标注导入到 Obsidian 指定位置，适合轻量级用户使用。

## 监控

1.  稍后读的加入、稍后读元数据的改变
2.  标注的加入、删除。
3.  标注色、标注元数据的改变。

## 前提

1.  此插件的版本为 1.2.0 版，如果你已经安装过这个插件，请需要升级，如何查看和升级请看下面。
2.  简悦需升级到 [高级账户](https://link.zhihu.com/?target=http%3A//simpread.pro/price)。
3.  事先安装好 Obsidian Local REST API 插件。

![](https://pic3.zhimg.com/v2-103a7bd993b3e37c4fd6b63321ea7c7e_r.jpg)

## 导入到 Obsidain 设置

### 下载

[导入到 Obsidian](https://link.zhihu.com/?target=https%3A//simpread.ksria.cn/plugins/details/1VQ19jCD8Z)

### 设置 Obsidian 库文件名

![](https://pic2.zhimg.com/v2-51aef4217947c190efb107ce70997609_r.jpg)

### 获取文件夹名称

文件夹建议事先建好

![](https://pic2.zhimg.com/v2-5a587b87780c58535cfa156c34d11ce1_r.jpg)

### 设置库名称与文件夹名称

![](https://pic4.zhimg.com/v2-a0a10ec8591f85d04b0101fd83c62f03_r.jpg)

### 获取 Local REST API

![](https://pic2.zhimg.com/v2-052e1b6981d03a9c6a963506001c2b99_r.jpg)

### 设置 Token

![](https://pic2.zhimg.com/v2-b2ddc2c8b9a155c2be99e159ffe1d1b1_r.jpg)

### 开启 Local REST API 导出方案

![](https://pic3.zhimg.com/v2-76529de238fd172b406bd55380041bca_r.jpg)

### 设置文件名

不要更改下面的内容

```
{{id}}-{{title}}{{mode}}
```

![](https://pic1.zhimg.com/v2-20b004a1b271f5347102bf9951248530_r.jpg)

### 自动化设置

开启下面的选项

![](https://pic1.zhimg.com/v2-878e5fe85b2e4ea813ba85e6da87b4a0_r.jpg)

### 保存

至此导入到 Obsidian 选项设置完毕，保存后刷新页面看下是否生效。（如不生效请看此页面下面的解决方案）

![](https://pic2.zhimg.com/v2-8151c705b075310b2e23aeadc6c17a45_r.jpg)

## 自动化辅助增强设置

### 下载插件

[自动化辅助增强](https://link.zhihu.com/?target=https%3A//simpread.ksria.cn/plugins/details/DH9l5jblPH)

### 配置

将下面的代码

```
o b u n
o b u u
o b a a
o b a c
```

加入到下图即可。

![](https://pic2.zhimg.com/v2-0aa22583e35c3b3c0f5ec1ecf378a90d_r.jpg)

### 保存

至此 [自动化辅助增强插件](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/discussions/3596) 完毕，保存后刷新页面看下是否生效。（如不生效请看此页面下面的解决方案）

![](https://pic2.zhimg.com/v2-0cf106742891d69e6c6375ce8da43239_r.jpg)

## 扩展端设置

扩展端仅需要设置下面的选项，位置在：选项页 → 服务 → 定制导出，同样此功能需要高级账户支持。

### 开启自定义标题

![](https://pic4.zhimg.com/v2-dc66f13d9719a1a7c3b1bdc9b2cb678f_r.jpg)

### 开启自定义 Markdown 模板

通过 [简悦官方模板库](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/discussions/2153) 随便找到一个 Obsidian 模板复制到下面即可。

![](https://pic4.zhimg.com/v2-672e1448553bf9ede9a4c074afbc809f_r.jpg)

## 最后

至此全部配置已经设置完毕，这些只是基础设置，此插件支持更多设置项，请前往 [文档查看](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/issues/2900)。

## 附录

### 与同步助手方案的区别

1.  同步助手方案是通过自动化将标注导入到 output 文件夹，支持阅读模式与稍后读使用，[配合 Hazel 自动化工具](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/discussions/3241)可不用开着 Obsidian。
2.  此插件不使用同步助手，但需要安装 Obsidian Local REST API 插件以及 [自动化辅助增强插件](https://link.zhihu.com/?target=https%3A//simpread.ksria.cn/plugins/details/DH9l5jblPH)，只能在阅读模式下使用，无法在稍后读下使用，适合不希望配置以及轻量级用户使用，无需 Hazel 等自动化工具但需要开着 Obsidian 才能导入成功。

### 插件安装失败等原因的解决方案

如果你没有使用同步助手的话，一般不会出现此问题，但发生无法安装插件等问题，请重置插件解决。

![](https://pic1.zhimg.com/v2-c3e544e525c37923d5d11d8a2b499b8c_r.jpg)

如果你使用了同步助手的话，请查看 [此解决方案](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/discussions/2342)，更多相关问题，请查看此 [问题清单](https://link.zhihu.com/?target=https%3A//github.com/Kenshin/simpread/discussions/categories/%25E9%2587%258D%25E8%25A6%2581%25E9%2580%259A%25E7%259F%25A5%3Fdiscussions_q%3Dcategory%253A%25E9%2587%258D%25E8%25A6%2581%25E9%2580%259A%25E7%259F%25A5%2B%25E6%258F%2592%25E4%25BB%25B6)。

### 如何升级插件

选项页 → 插件管理 → 导入到 Obsidain → 第三个 icon（或者直接 [重新安装](https://link.zhihu.com/?target=https%3A//simpread.ksria.cn/plugins/details/1VQ19jCD8Z)）。

![](https://pic3.zhimg.com/v2-a7b32b797644675ddeb9987caf3c36f6_r.jpg)