---
description: 记录 Hugo 博客搭建、本地管理、GitHub
  自动部署和日常发布流程。
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

## 一、目的

记录自己搭建 Hugo 博客的完整流程。

目标：

- 建立个人数字空间
- 使用 Markdown 管理文章
- 使用 Git + GitHub 自动发布网站
- 记录后续维护方法

------------------------------------------------------------------------

# 二、准备环境

## 本地环境

系统：

    Windows

博客目录：

```text
D:\Gudaobei-Blog
```

Hugo程序：

```text
D:\Hugo\hugo.exe
```

需要：

- Hugo Extended
- Git
- GitHub账号

------------------------------------------------------------------------

# 三、创建文章

进入博客目录：

```powershell
cd D:\Gudaobei-Blog
```

创建文章：

```powershell
D:\Hugo\hugo.exe new posts/文章名字.md
```

例如：

```powershell
D:\Hugo\hugo.exe new posts/我的第一篇文章.md
```

生成位置：

```text
content/posts/
```

------------------------------------------------------------------------

# 四、本地预览网站

进入博客目录：

```powershell
cd D:\Gudaobei-Blog
```

启动 Hugo：

```powershell
D:\Hugo\hugo.exe server
```

如果修改页面后没有及时刷新：

```powershell
D:\Hugo\hugo.exe server --disableFastRender
```

浏览器访问：

```text
http://localhost:1313
```

停止：

```text
Ctrl + C
```

------------------------------------------------------------------------

# 五、检查网站生成

正式生成：

```powershell
D:\Hugo\hugo.exe --minify
```

如果成功，会显示：

```text
Pages
Static files
Total in xxx ms
```

------------------------------------------------------------------------

# 六、Git 发布流程

每次修改文章、页面、样式后：

## 1. 查看变化

```powershell
git status
```

## 2. 添加所有修改

```powershell
git add .
```

## 3. 提交记录

例如：

```powershell
git commit -m "更新博客内容"
```

其他例子：

```powershell
git commit -m "新增技术文档"
```

```powershell
git commit -m "完善工具箱链接"
```

## 4. 上传 GitHub

```powershell
git push
```

------------------------------------------------------------------------

# 七、Git 常用命令

查看状态：

```powershell
git status
```

查看提交历史：

```powershell
git log
```

拉取远程更新：

```powershell
git pull
```

------------------------------------------------------------------------

# 八、GitHub 自动部署流程

工作流程：

    本地修改 Markdown
            ↓
    git add .
            ↓
    git commit
            ↓
    git push
            ↓
    GitHub Actions 自动运行
            ↓
    Hugo 生成网站
            ↓
    GitHub Pages 发布
            ↓
    自定义域名更新

------------------------------------------------------------------------

# 九、遇到的问题记录

## 1. Git提示身份未知

解决：

设置用户名：

```powershell
git config --global user.name "你的GitHub用户名"
```

设置邮箱：

```powershell
git config --global user.email "你的GitHub邮箱"
```

检查：

```powershell
git config --list
```

------------------------------------------------------------------------

## 2. Hugo构建失败

检查：

- Markdown格式
- Front Matter 的 `---`
- YAML格式是否正确

------------------------------------------------------------------------

## 3. GitHub Actions失败

检查：

```text
.github/workflows/hugo.yml
```

查看：

GitHub仓库：

    Actions

查看失败原因。

------------------------------------------------------------------------

# 十、日常发布习惯

以后写文章：

1. 创建 Markdown 文件
2. 本地预览
3. 修改确认
4. Git 上传

固定流程：

```powershell
git add .

git commit -m "更新博客"

git push
```

------------------------------------------------------------------------

# 更新记录

2026-10-14

完成 Hugo + GitHub Pages 自动部署流程整理。

后续补充：

- 主题优化
- 页面布局
- SEO设置
- 图片管理
