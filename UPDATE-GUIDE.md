# 星狐Claw 网站 & 部署工具 更新指南

> 本文档供 Claude 或开发者参考，包含完整的项目更新流程。

## 项目基本信息

| 项目 | 值 |
|------|------|
| **GitHub 仓库** | https://github.com/FoxProAI/xinghuclaw-tool |
| **分支** | `main` |
| **部署方式** | GitHub Pages（推送到 main 后自动部署） |
| **网站本地目录** | `c:\Users\1\Desktop\一键部署openclaw网站\一键部署1.0\website\` |
| **Manager 源码目录** | `c:\Users\1\Desktop\一键部署openclaw网站\一键部署1.0\openclaw-manager\` |
| **CDN 下载域名** | `dl.xinghuclaw.com`（Cloudflare R2 存储桶：`xinghuclaw-downloads`） |
| **当前 exe 版本** | `openclaw-manager-v46.exe` |

## 网站文件结构

```
website/
├── index.html      ← 首页（含下载按钮链接）
├── styles.css      ← 样式
└── UPDATE-GUIDE.md ← 本文档
```

---

## 场景 A：发布新版 exe（最常见）

当 openclaw-manager 有代码更新，需要发布新版本时：

### 1. 编译新版 exe

```bash
# 进入 manager 源码目录
cd "c:\Users\1\Desktop\一键部署openclaw网站\一键部署1.0\openclaw-manager"

# 构建前端
cd web && npx vite build && cd ..

# 编译 Go 二进制（替换 vXX 为新版本号）
go build -ldflags "-s -w -H windowsgui" -o build/openclaw-manager-vXX.exe .
```

### 2. 上传到 Cloudflare R2

- 登录 Cloudflare Dashboard → R2 对象存储 → `xinghuclaw-downloads`
- 上传 `build/openclaw-manager-vXX.exe`

### 3. 更新网站下载链接

编辑 `website/index.html`，找到下载按钮的 `href`（约第 33 行）：

```html
<!-- 将旧版本号改为新版本号 -->
<a href="https://dl.xinghuclaw.com/openclaw-manager-vXX.exe" class="download-btn">
```

### 4. 提交并推送

```bash
cd "c:\Users\1\Desktop\一键部署openclaw网站\一键部署1.0\website"
git add index.html
git commit -m "Update download link to vXX"
git push origin main
```

### 5. 更新本文档

将上方「当前 exe 版本」更新为新版本号。

---

## 场景 B：更新网站页面内容

修改 `index.html` 或 `styles.css` 后直接提交推送：

```bash
cd "c:\Users\1\Desktop\一键部署openclaw网站\一键部署1.0\website"
git add index.html styles.css
git commit -m "描述修改内容"
git push origin main
```

推送后 GitHub Pages 会自动重新部署，通常 1-2 分钟生效。

---

## 场景 C：仅更新 exe（不改网站）

如果新版 exe 文件名与旧版相同（覆盖上传到 R2），则无需修改网站代码。
直接在 Cloudflare R2 上传覆盖即可，CDN 缓存会自动更新。

---

## 关键路径速查

| 用途 | 路径 |
|------|------|
| 网站首页 | `website/index.html` |
| 网站样式 | `website/styles.css` |
| 下载链接位置 | `website/index.html` 约第 33 行 |
| Manager 前端源码 | `openclaw-manager/web/src/` |
| Manager Go 主程序 | `openclaw-manager/main.go` |
| 部署脚本生成 | `openclaw-manager/internal/gateway/controller.go` |
| 中文翻译 | `openclaw-manager/web/src/locales/zh.json` |
| 英文翻译 | `openclaw-manager/web/src/locales/en.json` |
| 构建脚本 | `openclaw-manager/build/build.bat` |
| Node.js 版本配置 | `openclaw-manager/internal/prereq/node_windows.go` (const nodeVersion) |

## Git 配置

- **用户名**: FoxProAI
- **邮箱**: 1737538918@qq.com
- **远程仓库**: origin → https://github.com/FoxProAI/xinghuclaw-tool.git
