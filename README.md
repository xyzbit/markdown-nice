<div align="center">
    <img alt="logo" src="https://raw.githubusercontent.com/caol64/wenyan/main/Data/256-mac.png" />
</div>

# Markdown Nice

[![npm](https://img.shields.io/npm/v/@xyzbit/markdown-nice)](https://www.npmjs.com/package/@xyzbit/markdown-nice)
[![License](https://img.shields.io/github/license/xyzbit/markdown-nice)](LICENSE)
[![Stars](https://img.shields.io/github/stars/xyzbit/markdown-nice?style=social)](https://github.com/xyzbit/markdown-nice)

> **本项目 Fork 自 [wenyan-core](https://github.com/caol64/wenyan-core)，感谢原作者的优秀工作！**

Markdown Nice 是一个 Markdown 排版美化和发布工具库，支持多种主题样式和一键发布到微信公众号等平台。

本项目是为 **[FlowDraft](https://github.com/xyzbit/FlowDraft)** AI 写作系统定制的 Markdown 渲染核心库。

## 功能

* 使用内置主题对 Markdown 内容排版
* 支持图片自动上传
* 支持数学公式渲染
* 一键发布文章到微信公众号草稿箱

## 主题效果

👉 [内置主题预览](https://yuzhi.tech/docs/wenyan/theme)

文颜采用了多个开源的 Typora 主题，在此向各位作者表示感谢：

- [Orange Heart](https://github.com/evgo2017/typora-theme-orange-heart)
- [Rainbow](https://github.com/thezbm/typora-theme-rainbow)
- [Lapis](https://github.com/YiNNx/typora-theme-lapis)
- [Pie](https://github.com/kevinzhao2233/typora-theme-pie)
- [Maize](https://github.com/BEATREE/typora-maize-theme)
- [Purple](https://github.com/hliu202/typora-purple-theme)
- [物理猫-薄荷](https://github.com/sumruler/typora-theme-phycat)
- [物理猫-Obsidian配色（Sakura / Radiation）](https://github.com/sumruler/obsidian-theme-phycat)
- 卡通紫（FlowDraft 自定义）
- 极客黑（FlowDraft 自定义）

## 安装方式

```bash
# 从 GitHub 安装（推荐用于 FlowDraft 项目）
npm install git+https://github.com/xyzbit/markdown-nice.git

# 或者从 NPM 安装（如果已发布）
npm install @xyzbit/markdown-nice
```

## 使用示例

### 1. Markdown 排版美化

```ts
import { getGzhContent } from "@xyzbit/markdown-nice/wrapper";

const inputContent = "# Hello, FlowDraft";
const theme = "lapis";
const highlightTheme = "solarized-light";
const isMacStyle = true;

const { title, cover, content, description } = await getGzhContent(
  inputContent,
  theme,
  highlightTheme,
  isMacStyle
);
```

#### 参数说明

| 参数名              | 类型        | 说明                                              |
| ---------------- | --------- | ----------------------------------------------- |
| `inputContent`   | `string`  | 输入的 Markdown 文本                                 |
| `theme`          | `string`  | 排版主题 ID（如 `"lapis"`, `"default"` 等，见下文）       |
| `highlightTheme` | `string`  | 代码高亮主题（如 `"github"`, `"solarized-light"`， 见下文） |
| `isMacStyle`     | `boolean` | 代码块是否启用 Mac 风格                   |

排版主题可选参数：

- default
- orangeheart
- rainbow
- lapis
- pie
- maize
- purple
- phycat
- phycat_sakura
- phycat_radiation
- cartoon_purple
- geek_black

高亮主题可选参数：

- atom-one-dark
- atom-one-light
- dracula
- github-dark
- github
- monokai
- solarized-dark
- solarized-light
- xcode

#### 返回值

| 字段            | 类型       | 说明                     |
| ------------- | -------- | ---------------------- |
| `title`       | `string` | `frontmatter`中的文章标题，见下文 |
| `cover`       | `string` | `frontmatter`中的文章封面图，见下文          |
| `content`     | `string` | 转换后的 HTML 文章内容，发布接口需要用到         |
| `description` | `string` | `frontmatter`中的文章简介，见下文         |

---

### 2. 发布到微信公众号草稿箱

```ts
import { publishToDraft } from "@xyzbit/markdown-nice/publish";

// 方式1，你可以通过环境变量注入WECHAT_APP_ID和WECHAT_APP_SECRET
const wechatAppId = process.env.WECHAT_APP_ID;
const wechatAppSecret = process.env.WECHAT_APP_SECRET;

if (!wechatAppId || !wechatAppSecret) {
  console.error("WECHAT_APP_ID and WECHAT_APP_SECRET must be set as environment variables.");
  process.exit(1);
}

const data = await publishToDraft(title, content, cover);

if (data.media_id) {
  console.log(`上传成功，media_id: ${data.media_id}`);
} else {
  console.error(`上传失败，\n${data}`);
}

// 方式2，你可以直接以参数形式传入WECHAT_APP_ID和WECHAT_APP_SECRET

const data = await publishToDraft(title, content, cover, wechatAppId, wechatAppSecret);
```

#### 参数说明

| 参数名       | 类型       | 说明         |
| --------- | -------- | ---------- |
| `title`   | `string` | 文章标题       |
| `content` | `string` | 文章 HTML 内容 |
| `cover`   | `string` | 封面图 URL    |

#### 返回值

返回 **微信公众号 API 的响应对象**，常见字段：

| 字段         | 类型       | 说明                    |
| ---------- | -------- | --------------------- |
| `media_id` | `string` | 草稿的 media\_id，后续发布时需要 |

## 环境变量

在使用 `publishToDraft` 前，需要在环境中配置：

* `WECHAT_APP_ID`
* `WECHAT_APP_SECRET`

推荐通过 `.env` 文件或 CI/CD 环境变量注入。

## 浏览器直接使用

除了通过 `npm` 安装外，你也可以直接在浏览器环境中引入打包好的版本（IIFE 格式），无需构建工具。

推荐使用 **[unpkg](https://unpkg.com/)** 或 **[jsDelivr](https://www.jsdelivr.com/)**。

浏览器版本需自行引入依赖`highlight.js`和`csstree`。

```html
<!-- 添加依赖 -->
<script src="https://cdn.jsdelivr.net/npm/css-tree/dist/csstree.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.11.1/highlight.min.js"></script>

<!-- 从 unpkg 引入 -->
<script src="https://unpkg.com/@xyzbit/markdown-nice/dist/math/markdown-nice-math.js"></script>
<script src="https://unpkg.com/@xyzbit/markdown-nice/dist/styles/markdown-nice-styles.js"></script>
<script src="https://unpkg.com/@xyzbit/markdown-nice/dist/browser/markdown-nice.js"></script>

<script>
  // 使用全局变量 MarkdownNice
  const { configureMarked, renderMarkdown, themes } = MarkdownNice;

  (async () => {
    configureMarked();
    const input = "# Hello from FlowDraft";
    const content = await renderMarkdown(input);
    const theme = themes["lapis"];
    const styledCss = await theme.getCss();
    const style = document.createElement("style");
    style.textContent = styledCss;
    document.head.appendChild(style);
    document.body.innerHTML = content;
  })();
</script>
```

这样你就可以在 **任意前端项目** 或 **纯静态页面** 中直接使用 Markdown Nice 的功能。

## 微信公众号 IP 白名单

请务必将服务器 IP 加入公众号平台的 IP 白名单，以确保上传接口调用成功。
详细配置说明请参考：[https://yuzhi.tech/docs/wenyan/upload](https://yuzhi.tech/docs/wenyan/upload)

## 配置说明（Frontmatter）

为了可以正确上传文章，需要在每一篇 Markdown 文章的开头添加一段`frontmatter`，提供`title`、`cover`两个字段：

```md
---
title: 在本地跑一个大语言模型(2) - 给模型提供外部知识库
cover: /Users/lei/Downloads/result_image.jpg
description: 本文介绍如何为本地大语言模型提供外部知识库。
---
```

* `title` 是文章标题，必填。
* `cover` 是文章封面，支持本地路径和网络图片：

  * 如果正文有至少一张图片，可省略，此时将使用其中一张作为封面；
  * 如果正文无图片，则必须提供 cover。

## 关于图片自动上传

* 支持图片路径：

  * 本地路径（如：`/Users/lei/Downloads/result_image.jpg`）
  * 网络路径（如：`https://example.com/image.jpg`）

## 致谢

本项目基于 [wenyan-core](https://github.com/caol64/wenyan-core) 开发，感谢原作者 [@caol64](https://github.com/caol64) 的优秀工作！

文颜采用了多个开源的 Typora 主题，在此向各位作者表示感谢：

- [Orange Heart](https://github.com/evgo2017/typora-theme-orange-heart)
- [Rainbow](https://github.com/thezbm/typora-theme-rainbow)
- [Lapis](https://github.com/YiNNx/typora-theme-lapis)
- [Pie](https://github.com/kevinzhao2233/typora-theme-pie)
- [Maize](https://github.com/BEATREE/typora-maize-theme)
- [Purple](https://github.com/hliu202/typora-purple-theme)
- [物理猫-薄荷](https://github.com/sumruler/typora-theme-phycat)
- [物理猫-Obsidian配色（Sakura / Radiation）](https://github.com/sumruler/obsidian-theme-phycat)
- 卡通紫（FlowDraft 自定义）
- 极客黑（FlowDraft 自定义）

## License

Apache License Version 2.0
