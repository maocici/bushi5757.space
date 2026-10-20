# site-starter — 练手用静态站

## 用途

一个零依赖的静态站骨架，用来跑通「托管 → 访问 → 绑域名」这条链路。

- **零外部依赖**：没有 Google Fonts、没有 CDN 上的 JS/CSS。整站只有本地文件，国内加载不会因为境外资源而卡白。
- **自带深浅色**：跟随系统设置，不用额外配置。
- **手机可用**：响应式，窄屏不会错位。

## 文件

| 文件 | 作用 |
|---|---|
| `index.html` | 首页。要改的只有注释标出的那一段 |
| `404.html` | 访问不存在的路径时显示 |
| `robots.txt` | 允许搜索引擎收录 |

## 用法

### 方式 A：Cloudflare Pages（推荐）

1. 注册 Cloudflare 账号 → **Workers & Pages** → 创建项目 → **直接上传**
2. 把 `site-starter` 这个**文件夹**整个拖进去
3. 上传完会立刻给你一个 `xxx.pages.dev` 网址 —— 这时候网站已经能被访问了
4. 想绑自己的域名：项目 → **Custom domains** → 添加域名
   - 绑**根域**（`你的域名`）需要先把域名的 NS 改成 Cloudflare 的
   - 绑**子域**（`www.你的域名`）只需加一条 CNAME

> ⚠️ 顺序：**先在 Pages 里添加域名，再配 DNS**。反了会报 522。

### 方式 B：GitHub Pages（不动 NS）

1. 新建一个**公开**仓库，把这三个文件传到根目录
2. Settings → Pages → 选分支 → 保存
3. 得到 `你的用户名.github.io/仓库名` 的网址
4. 想绑自己的域名：Settings → Pages → Custom domain 填域名并保存
5. 去阿里云云解析加记录：

| 类型 | 主机记录 | 记录值 |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `你的用户名.github.io` |

6. 回 GitHub 勾上 **Enforce HTTPS**（这个选项可能要等最多 24 小时才出现）

> ⚠️ 顺序：**先在 GitHub 里填域名，再配 DNS**。反了可能被别人抢注你的子域。

## 改内容

朋克风格首页，只改 `index.html` 里这几处：

**1. 名字** —— 两个地方必须同时改，否则故障（glitch）效果会错位：

```html
<h1 class="name" data-t="maocici">maocici</h1>
```

`data-t` 和标签内的文字要完全一致。

**2. 简介** —— `.sub` 那一段，`<b>` 里的词显示为荧光绿。

**3. 联系方式** —— 每个 `.lnk` 里改**两处**：`href` 和 `<span>` 文字要对应（例如 `mailto:` 后的地址和显示的地址要一致）。

**4. 顶栏** —— `.top` 里的 `MAOCICI // SYS.ONLINE`。

### 以后要加「项目」区块

在 `<hr class="hr" />` 之后、`联系` 之前插入：

```html
<p class="label">项目</p>
<div class="links">
  <a class="lnk" href="#">
    <span>项目名 · 一句话说明</span>
    <span class="k">ESP32</span>
  </a>
  <a class="lnk" href="#">
    <span>项目名 · 一句话说明</span>
    <span class="k">JETSON</span>
  </a>
</div>
```

改完重新上传（Cloudflare 直传重拖一次，或 GitHub 重新 commit）即可。

## 日常更新内容

改完文件后，按你选的方式重新发布。三种方式按上手难度排列：

| 方式 | 怎么做 | 适用 | 代价 |
|---|---|---|---|
| ① 拖文件夹 | Cloudflare Pages → Create new deployment → 重拖文件夹 | 起步阶段 | 每次传全部，看不到 diff |
| ② 网页改文件 | GitHub 网页打开 `index.html` → 铅笔图标 → 编辑 → Commit | 改几个字最快，手机也能改 | 文件得先放在 GitHub |
| ③ `git push` | 本地 `git add` / `commit` / `push`，平台自动部署 | 长期、内容多、要版本历史 | 要花半小时学 git |

**推荐路径：起步用 ① → 改过 5 次之后自然想转 ③ → ② 留作应急。**

### 本地预览（改之前先看效果）

直接**双击 `index.html`** 用浏览器打开就行。纯静态页面在 `file://` 下完全正常，不需要起服务器。

### 图片

- 放同目录，或建 `images/` 子目录，HTML 里写 `<img src="images/a.jpg" alt="说明">`
- **压到 200 KB 以内**，优先 `.webp`
- 文件名用**英文小写 + 连字符**，不要中文和空格（URL 编码会出问题）

### 改完没生效？

1. 强刷：`Ctrl + F5`
2. 等 1 分钟（CDN 缓存）
3. 用无痕窗口 / 手机流量复测

### 什么时候该换方案

- 每次都要复制粘贴整篇文章、开始觉得烦 → 上静态站生成器（Hugo / Astro / VitePress）
- 想要后台点按钮发布 → Git 集成 + Headless CMS（如 Pages CMS）
- **在那之前，直接改 HTML 就够了**，别提前上工具

## 验证上线

```powershell
# 看解析是否生效（Windows 没有 dig，用这个）
Resolve-DnsName 你的域名

# 用 curl 看 HTTP 状态码和证书
curl.exe -I https://你的域名
```

再用**手机流量**（关 WiFi）访问一次，那是访客的真实视角。

## 回滚

- **Cloudflare Pages**：项目 → Deployments → 选上一个版本 → Rollback（保留历史，可随时回）
- **GitHub Pages**：`git revert` 或直接改回旧文件重新提交
- **彻底不要了**：删掉项目/仓库，把域名的 DNS 记录删掉即可，不影响域名本身

## 备注

- 练手阶段**不需要**：服务器、备案、SSL 证书、AI 建站包
- 平台免费子域（`*.pages.dev`、`*.github.io`）在国内访问可能不稳定，**对外公布时一律用自己的域名**
