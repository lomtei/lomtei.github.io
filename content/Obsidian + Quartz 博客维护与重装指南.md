---
share: true
title: Obsidian + Quartz 博客维护与重装指南
date: 2025-12-19 01:00
tags:
---

## 1. 核心原理（以此为据）

我的博客系统由两部分组成，采用了 **“物理隔离”** 的安全模式：

*   **本地 (Obsidian)**：这是**母库**。包含所有私人数据（备课、日记、小说）。只有在 `50_Creative_Studio/Blog` 下的笔记，且带有 `share: true` 标记的，才会被发送出去。
*   **云端 (GitHub + Quartz)**：这是**子库**。只存放公开的博客文章。它负责把 Markdown 变成漂亮的网页。

**关键连接器**：
*   插件：**Enveloppe** (原名 GitHub Publisher)
*   钥匙：**GitHub Token** (个人访问令牌)

---

## 2. 日常写作流程 (Daily Workflow)

### 写文章
1.  在 `50_Creative_Studio/Blog` 文件夹下新建笔记。
2.  **插入模版**：按下快捷键 (如 `Alt + Q`)，选择博客模版。
    *   *确保头部包含 `share: true`*。
3.  正常写作，可随意插入图片。

### 发文章
1.  **开启 VPN**：必须开启，且建议选择**日本/新加坡/台湾**等非拥堵节点（防止 401/Rate Limit 报错）。
2.  **一键发送**：
    *   按 `Ctrl + P` 调出命令面板。
    *   输入 `active`，选择 **`Enveloppe: Upload single current active note`**。
3.  **等待**：右上角弹出 `Success` 后，等待约 2 分钟。
4.  **查看**：访问 `https://lomtei.github.io`。

> [!WARNING] 注意事项
> 如果网页显示 404 或内容未更新，请先尝试 **Ctrl + F5** 强制刷新浏览器缓存。

---

## 3. 新电脑/重装环境指南 (Disaster Recovery)

如果换了新电脑，或者 Obsidian 崩溃重装，请按此步骤恢复**“发布功能”**。

### 第一步：安装插件
1.  在 Obsidian 插件市场搜索并安装 **`Enveloppe`** (作者 Mara-Li)。
2.  启用插件。

### 第二步：配置插件 (Enveloppe)
打开插件设置，严格核对以下信息：

#### 1. GitHub Config (账号设置)
*   **GitHub Username**: `lomtei`
*   **Repository Name**: `lomtei.github.io`
*   **GitHub Token**: *需要重新申请（见下文“常见问题”）*。
*   **Main branch**: **`v4`** (❌ 注意：不要填 main，这是我们遇到过的坑)。

#### 2. File paths (路径设置)
*   **Folder behavior**: 选择 `Fixed Folder`。
*   **Root folder**: 填入 **`content`**。

#### 3. Attachment & embeds (附件设置)
*   **Folder behavior**: 选择 `Fixed Folder`。
*   **Default attachment folder**: 填入 **`attachments`**。

### 第三步：恢复模版
1.  新建文件夹 `99_Templates`。
2.  新建笔记 `博客模板`，填入以下内容：
    ```markdown
    ---
    share: true
    title: {{title}}
    date: {{date}} {{time}}
    tags: 
    ---
    ```
3.  在核心插件“模板”中，指向这个文件夹。
4.  在“快捷键”设置中，给“插入模板”绑定快捷键。

---

## 4. 从零开始搭建 (Rebuild from Scratch)

如果你的 GitHub 仓库（lomtei.github.io）被删了，需要彻底重来，请按此步骤：

### 阶段一：GitHub 端准备
1.  **Fork 仓库**：去 `jackyzha0/quartz` 项目，点击 Fork。
2.  **改名**：将 Fork 下来的仓库改名为 `lomtei.github.io`。
3.  **开权限 (Actions)**：
    *   Settings -> Actions -> General -> Workflow permissions。
    *   改为 **Read and write permissions** -> Save。
4.  **补全部署文件** (❌ 容易遗忘)：
    *   在仓库新建文件 `.github/workflows/deploy.yml`。
    *   复制粘贴标准部署代码（参考 Quartz 官方或之前的备份）。
5.  **修改 Pages 源**：
    *   Settings -> Pages -> Source 改为 **GitHub Actions**。

### 阶段二：修改配置 (Config)
在 GitHub 网页端直接修改以下文件：
1.  **`quartz.config.ts`**:
    *   `baseUrl`: 改为 `lomtei.github.io`。
    *   `locale`: 改为 `zh-CN`。
    *   `pageTitle`: 改为你的博客名。
2.  **`quartz.layout.ts`**:
    *   在 `right: []` 中加入 `Component.RecentNotes({ title: "最新文章", limit: 5 }),` 以实现自动列表。

### 阶段三：初始化主页
1.  在 Obsidian 里写一个 **`index.md`** (带 share: true)。
2.  上传它。

---

## 5. 故障排除手册 (Troubleshooting)

这是我们共同踩过的坑，遇到问题先查这里：

### Q1: 上传时报错 `401 Unauthorized` 或 `Rate limit exceeded`
*   **原因**：网络问题，IP 被 GitHub 封锁。并不是密码错了。
*   **解决**：
    1.  切换 VPN 节点（不要用美国节点，改用冷门节点）。
    2.  重启 Obsidian。
    3.  重试上传。

### Q2: 上传时报错 `Branch not found`
*   **原因**：分支名填错了。
*   **解决**：检查 Enveloppe 设置里的 `Main branch`，Quartz 4.0 默认是 **`v4`**，不是 main。

### Q3: 网页显示 XML 代码，没有样式
*   **原因**：`quartz.config.ts` 里的 `baseUrl` 没改对。
*   **解决**：去 GitHub 修改该文件，将 `quartz.jzhao.xyz` 改为 `lomtei.github.io`。

### Q4: 网页显示 404 (GitHub Pages)
*   **原因 A**：没有上传 `index.md`。
    *   *解法*：新建 `index.md` 并上传。
*   **原因 B**：GitHub Actions 失败。
    *   *解法*：去仓库的 Actions 页面，查看报错。如果是 "Jekyll" 报错，说明 Pages Source 没改成 "GitHub Actions"。

### Q5: 怎么申请新 Token？
如果换电脑需要新 Token：
1.  GitHub -> Settings -> Developer settings -> Personal access tokens (classic)。
2.  Generate new token。
3.  **关键点**：必须勾选 **`repo`** (全选) 权限。