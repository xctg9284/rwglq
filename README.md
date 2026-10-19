# 档案管理系统 · GitHub Pages 部署包

版本：**2026.10.18-b14**（程序「设置」面板顶部可核对）

## ⚠️ 解压后是几层？拖哪一层？

解压后会得到一个 `github-deploy/` 文件夹，里面是：

```
github-deploy/              ← 外层文件夹本身【不要】传
├── public/index.html           ← 程序本体
├── .github/workflows/pages.yml ← 自动部署配置
├── .nojekyll                   ← 空文件，必须有
└── README.md                   ← 本说明（可传可不传）
```

**要传的是里面的内容，不是 `github-deploy` 这个文件夹本身。**

传完后仓库根目录应该长这样：

```
仓库根目录/
├── public/index.html
├── .github/workflows/pages.yml
└── .nojekyll
```

### 为什么不能连外层一起传

GitHub 只认**位于仓库根目录**的 `.github/workflows/`。如果变成
`github-deploy/.github/workflows/pages.yml`，它不会被识别成工作流，
Actions 标签页里一个工作流都不会出现，也就不会部署。

> 至于 `index.html` 放在 `public/` 里不用担心——pages.yml 会自动在仓库里
> 搜索 index.html，不管它在根目录还是子目录都能找到并发布。

### 传错了怎么办

把仓库里的 `github-deploy/` 整个删掉，重新按上面的结构传一遍即可，没有副作用。

---

## 部署步骤

### 方式 A：上传文件（最简单，不需要 Actions）

1. 新建仓库，选 **Public**（私有仓库的 Pages 要付费）
2. 上传 `public/index.html`（**文件名必须是 index.html**，中文名或别的名字会 404）
3. `Settings → Pages → Build and deployment → Source` 选 **Deploy from a branch**
4. Branch 选 `main`，文件夹选 `/ (root)` → Save
5. 等 1~3 分钟，顶部出现 `https://用户名.github.io/仓库名/`

### 方式 B：用 GitHub Actions（本包已配好）

1. 整个按上面的目录结构上传（含 `.github/workflows/pages.yml`）
2. `Settings → Pages → Source` 选 **GitHub Actions**（不是 Deploy from a branch）
3. `Actions` 标签页里跑一次工作流，或 push 一次自动触发
4. 得到网址

> 若 Actions 报 `Failed to create deployment (status: 404)`，
> **99% 是 Source 没切到 GitHub Actions**，回去改一下再重跑。

---

## 常见坑

| 现象 | 原因 / 怎么改 |
|---|---|
| 打开是 404 | 文件名不是 `index.html`，或仓库是 Private |
| Actions 报 404 | Source 没选「GitHub Actions」 |
| 改了还是旧内容 | Pages 有 CDN 缓存，等 1~3 分钟；强制刷新 Ctrl+F5 |
| 微信打不开 | github.io 偶被微信拦截，改用腾讯云 EdgeOne Pages 或绑定已备案域名 |
| 国内访问慢 | github.io 时有 DNS 污染。建议**同时**部署一份到 EdgeOne Pages 备用 |

`.github` 是隐藏目录，网页上传可能拖不进去。最省事的办法：先传
`public/index.html`，再点 **Add file → Create new file**，文件名直接敲
`.github/workflows/pages.yml`（GitHub 会自动建目录），内容粘贴进去。
`.nojekyll` 同理，内容留空。

---

## 关于页面托管在哪（国内用户请看）

程序是**一个 HTML 文件**，没有后端，托管在哪都行。三部分走的是不同链路：

| 内容 | 走哪条链路 | 国内体验 |
|---|---|---|
| 页面本身（500 KB，只加载一次） | 你选的静态托管 | 慢一点也能忍，加载完就缓存 |
| **云同步**（Gist API） | `api.github.com` | **与页面托管无关**，换托管不会改善 |
| **图片**（大头） | 腾讯云 COS | 国内节点，快 ✅ |

所以：换托管**只能解决"打开页面"的问题**，同步链路仍走 GitHub，
图片已经走国内 COS。要彻底改善同步体验，得换同步后端。

> Gitee Pages 已于 2024 年下线，CODING Pages 已于 2025-09-01 下线。
> 网上很多旧教程还在推荐，别照着做。
