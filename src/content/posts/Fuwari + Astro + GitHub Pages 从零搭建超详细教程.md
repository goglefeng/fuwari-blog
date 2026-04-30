---

title: Fuwari + Astro + GitHub Pages 从零搭建超详细教程

published: 2026-04-30

description: 帮助你从零开始搭建个人博客

tags: [Markdown, Blogging, Demo]

category: Examples

draft: false

---

本教程手把手带你从零完成 **Fuwari 主题 + Astro 框架 + GitHub Pages** 个人博客全套搭建，从环境安装、模板初始化、本地调试、基础个性化配置，到 GitHub Actions 自动部署全程覆盖。步骤清晰、命令可直接复制复用，不仅适合新手一键复刻搭建，也方便自己后续遗忘流程时快速回看复盘，轻松拥有免费、稳定、高颜值的个人技术博客。

------

# 一、环境准备（只做一次、必装工具）

## 1.Node.js（必须 LTS）

https://nodejs.org/

安装后检查：

```bash
node -v
npm -v
```

------

## 2.Git

https://git-scm.com/

安装后检查：

```bash
git --version
```

------

## 3. pnpm（重点，Fuwari推荐）

```bash
npm install -g pnpm
```

安装后检查：

```bash
pnpm -v
```

------

## 4.VSCode（推荐）

插件建议：

- Astro
- Prettier
- ESLint

------

# 二、本地启动（先确保项目能跑）

------

## 1.克隆项目

```bash
git clone https://github.com/saicaca/fuwari.git my-blog
cd my-blog
```

------

## 2. 安装依赖（必须 pnpm）

```bash
pnpm install
```

------

## 3.启动本地服务

```bash
pnpm dev
```

访问：

```text
http://localhost:4321
```

看到页面 = 成功 ✅

------

# 三、修改配置（决定你会不会白屏）

打开文件：

```
astro.config.mjs
```

找到这一段，改成（按你自己的信息填）

```
export default defineConfig({
  site: "https://你的用户名.github.io",
  base: "/你的仓库名/",
});
```

------

## 举例

你用户名是：

```
goglefeng
```

仓库名我们用：

```
fuwari-blog
```

那就写：

```
site: "https://goglefeng.github.io",
base: "/fuwari-blog/",
```

------

 **为什么必须改？**

GitHub Pages 的访问路径是：

```
https://用户名.github.io/仓库名/
```

不改就会：

❌ 页面空白
 ❌ 样式丢失

# 四、GitHub Actions 自动部署（核心）

## 1.创建文件：

```text
.github/workflows/deploy.yml
```

------

## 2.正确 pnpm 版本配置

```yml
name: Deploy Astro to GitHub Pages

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Enable pnpm
        run: npm install -g pnpm

      - name: Install dependencies
        run: pnpm install

      - name: Build project
        run: pnpm build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

---

# 五、GitHub 仓库创建（非常关键）

------

## 1.仓库命名规范

###  	推荐方式（子项目）

```text
my-blog
```

### 	进阶方式（个人主页）

```text
username.github.io
```

------

## 2.选择区别

| 类型               | 作用       |
| ------------------ | ---------- |
| username.github.io | 直接主站   |
| my-blog            | 子路径站点 |

------

# 六、项目初始化 & 推送

------

## 1. 初始化 git

```bash
git init
git add .
git commit -m "init blog"
```

------

## 2. 绑定远程仓库

```bash
git branch -M main
git remote add origin https://github.com/你的用户名/仓库名.git
git push -u origin main
```

------

# 七、GitHub Pages 配置

## 1.进入：

```text
GitHub → Settings → Pages
```

## 2.设置：

```text
Source: GitHub Actions
```

------

# 八、上线流程（验证是否成功）

------

## 1. 推送代码

```bash
git add .
git commit -m "deploy"
git push
```

------

## 2.查看部署

GitHub：

```text
Actions → running → success
```

------

## 3. 访问网站

子路径模式：

```text
https://用户名.github.io/my-blog/
```

------

✔ 能打开页面 = 成功上线 🎉

------

# 九、写第一篇博客

------

## 1.路径：

```text
src/content/posts/
```

## 2.创建文件：

```text
hello.md
```

------

## 3.内容：

```md
---
title: Expressive Code Example	\\标题
published: 2024-04-10	\\时间
description: How code blocks look in Markdown using Expressive Code.	\\文章简介
tags: [Markdown, Blogging, Demo]	\\标签，给文章加关键字
category: Examples	\\分类
draft: false	\\正常显示，true不显示
---

这是我的博客第一篇文章 🚀
```

------

# 十、日常使用流程（以后只需要记这个）

------

## 1.写文章

```text
src/content/posts/
```

------

## 2.本地预览

```bash
pnpm dev
```

------

## 3. 发布上线

```bash
git add .	//把你本地所有修改的文件，标记为 “待保存”
git commit -m "new post"	//生成一个本地版本，做一个保存点
git push	//把本地保存的版本，上传到 GitHub
```

------

## 4.自动部署

GitHub Actions 自动完成（不用管）





---

# 十一、错误

---

## 1.上传错误排查

```
进入github仓库--进去Actions--左侧Deploy Astro to GitHub Pages 

此错误为pnpm 版本冲突
```

![image-20260430224851713](https://cdn.jsdelivr.net/gh/goglefeng/blog-images@main/fuwari/20260430224851806.png)

修改步骤六中创建的文件

```
.github/workflows/deploy.yml
```

 找到这段

```
- uses: pnpm/action-setup@v4
  with:
    version: 8
```

 改成这样（关键）

```
- uses: pnpm/action-setup@v4
```

把 `version: 8` 整行删掉

---

## 2.版本回退

查询自己的github名字

```
git config user.name	//这里查自己github设置的名字
```

显示goglefeng提交的版本

```
git log --oneline --author="goglefeng"  //这里换成查到的名字
```

回退到621fcd2版本

```
git reset --hard 621fcd2	//替换为查询到的
```

本地操作后再同步到 GitHub 即可：

1. 先在本地回退到之前的版本（用上面的 `git reset` 命令）

2. 强制推送到 GitHub：

   ```
   git push -f
   ```

⚠️ 注意：`-f` 强制推送会覆盖 GitHub 上的历史记录，只在你自己的仓库用，团队项目别随便用。

------

# 最终总结（一句话版）

```text
装环境 → 克隆模板 → pnpm启动 → 配置GitHub → push → Actions部署 → 上线 → 写文章
```