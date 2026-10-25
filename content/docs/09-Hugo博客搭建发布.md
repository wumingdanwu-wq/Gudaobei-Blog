---
description: Hugo 博客与 Git 发布操作手册：写文章、预览、推送、同步、常见报错速查。
draft: false
hideMeta: true
ShowToc: true
slug: hugo-git
title: Hugo 博客搭建与 Git 发布
weight: 9
---

# Hugo 博客搭建与 Git 发布

[返回技术文档目录](/docs/)

[返回工具合集](/tools/)

> 操作手册：打开就能照着敲。所有命令都在 PowerShell 里执行。

## 速查：写完文章后的三步

```powershell
cd D:\Gudaobei-Blog
git pull                      # 写之前先拉取
# ……写文章、预览……
git add .
git commit -m "更新博客"
git push
```

推送后，GitHub Actions 会自动构建并发布网站，不需要手动部署。

------------------------------------------------------------------------

## 一、环境与路径

| 项目         | 内容                                              |
| ------------ | ------------------------------------------------- |
| 系统         | Windows                                           |
| 博客目录     | `D:\Gudaobei-Blog`                                |
| Hugo 程序    | `D:\Hugo\hugo.exe`（未加入 PATH，必须写完整路径） |
| 需要的软件   | Hugo Extended、Git、GitHub 账号                   |
| 文章位置     | `content/posts/`                                  |
| 技术文档位置 | `content/docs/`                                   |
| 自动部署配置 | `.github/workflows/hugo.yml`                      |

------------------------------------------------------------------------

## 二、写文章与预览

**1. 进入博客目录**

```powershell
cd D:\Gudaobei-Blog
```

**2. 新建文章**

```powershell
hugo new posts/文章名字/index.md
```

文件生成在 `content/posts/`。

**3. 本地预览**

```powershell
D:\Hugo\hugo.exe server
```

- 浏览器打开：http://localhost:1313
- 改了页面没刷新：`D:\Hugo\hugo.exe server --disableFastRender`
- 停止预览：`Ctrl + C`
- 想看草稿（`draft: true`）：加 `-D`，即 `D:\Hugo\hugo.exe server -D`

**4. 推送前检查构建**

```powershell
D:\Hugo\hugo.exe --minify
```

出现 `Pages`、`Static files`、`Total in xxx ms` 表示成功；出现 `ERROR` 就先别推送。

------------------------------------------------------------------------

## 三、日常发布流程

```powershell
git pull                          # 1. 先拉取远程最新内容
git status                        # 2. 查看改了哪些文件
git add .                         # 3. 添加所有修改
git commit -m "说明这次改了什么"    # 4. 提交
git push                          # 5. 上传到 GitHub
```

提交说明的例子：

```text
更新博客内容
新增技术文档
完善工具箱链接
```

其他常用命令：

```powershell
git log                 # 查看提交历史
git log --oneline       # 一行一条，更简洁
```

------------------------------------------------------------------------

## 四、首次把本地项目推送到 GitHub

```powershell
git init                          # 已经有 .git 就跳过
git remote add origin https://github.com/用户名/仓库名.git
git branch -M main
git add .
git commit -m "init: hugo site"
git push -u origin main
```

- 远程地址写错：`git remote set-url origin 新地址`
- 查看当前远程地址：`git remote -v`
- 推送被拒绝（远程有 README 等本地没有的提交）：

```powershell
git pull origin main --allow-unrelated-histories
git push
```

------------------------------------------------------------------------

## 五、从 GitHub 同步到本地

**情况 A：本地没有这个项目（换电脑、重装系统）**

```powershell
git clone https://github.com/用户名/仓库名.git
cd 仓库名
```

主题如果是 submodule，克隆时加上子模块：

```powershell
git clone --recurse-submodules https://github.com/用户名/仓库名.git
```

**情况 B：本地已有项目，只想拉取最新内容**

```powershell
git pull origin main
```

已经克隆但没拉到子模块：

```powershell
git submodule update --init --recursive
```

换电脑后还要：重新安装 Hugo Extended 和 Git，并重新设置身份（见下方“常见问题”第 1 条）。

------------------------------------------------------------------------

## 六、自动部署流程

```text
本地修改 Markdown
        ↓
git add . → git commit → git push
        ↓
GitHub Actions 自动运行（仓库 Actions 页面可查看）
        ↓
Hugo 生成网站
        ↓
GitHub Pages 发布
        ↓
自定义域名更新
```

推送后去仓库的 **Actions** 页面，最新一次运行出现绿色对勾就是发布成功；网站没变化时按 `Ctrl + F5` 强制刷新。

------------------------------------------------------------------------

## 七、改文件名或删文件时

文件名被别的页面用 `relref` 引用时，直接改名会导致构建失败。

```powershell
# 1. 用 git mv 改名（Git 会识别为重命名）
git mv "content/docs/旧文件名.md" "content/docs/新文件名.md"

# 2. 搜索哪些地方还在引用旧名字（没有输出就表示没有）
Select-String -Path content\*\*.md -Pattern "旧文件名" -Encoding UTF8

# 3. 把搜到的引用都改成新名字，然后本地构建检查
D:\Hugo\hugo.exe --minify

# 4. 没有报错再提交推送
git add .
git commit -m "重命名文档"
git push
```

------------------------------------------------------------------------

## 八、常见问题速查

| 现象                                           | 原因与处理                                                                                                              |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `hugo : 无法将“hugo”项识别为 cmdlet`           | Hugo 没加入 PATH，改用完整路径 `D:\Hugo\hugo.exe`                                                                       |
| Git 提示身份未知（Please tell me who you are） | 设置用户名和邮箱，见下方命令                                                                                            |
| `rejected` / `non-fast-forward`                | 远程比本地新：`git pull --rebase origin main`，再 `git push`                                                            |
| `nothing to commit`                            | 没有改动，不用提交                                                                                                      |
| 推送时要求登录                                 | GitHub 不支持密码登录，用 Personal Access Token 或 Git Credential Manager（会弹出浏览器授权）                           |
| Hugo 构建失败                                  | 检查 Markdown 格式、Front Matter 两条 `---` 是否成对、YAML 缩进和冒号后空格                                             |
| `REF_NOT_FOUND`                                | `relref` 链接指向的文件不存在，检查文件名，见第七节                                                                     |
| GitHub Actions 失败                            | 到仓库 Actions 页面看失败日志；检查 `.github/workflows/hugo.yml`；确认 Settings → Pages 的 Source 选的是 GitHub Actions |
| `LF will be replaced by CRLF`                  | 只是换行符提示，不影响，可忽略                                                                                          |
| `WARN deprecated: .Language.LanguageCode`      | 主题用了即将淘汰的写法，只是警告，暂时可忽略                                                                            |

**设置 Git 身份：**

```powershell
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub邮箱"
git config --list               # 检查是否生效
```

------------------------------------------------------------------------

## 更新记录

- 2026-10-14：完成 Hugo + GitHub Pages 自动部署流程整理。
- 2026-10-18：整理为操作手册，合并 Git 三种流程（首次推送、同步到本地、日常推送），补充改名检查和常见问题速查。

后续补充：

- 主题优化
- 页面布局
- SEO 设置
- 图片管理
