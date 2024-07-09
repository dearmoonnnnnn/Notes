- [零、概念和问题](#零概念和问题)
  - [概念](#概念)
        - [1. GFM](#1-gfm)
  - [问题](#问题)
        - [1. 生成的目录在手机端无法跳转](#1-生成的目录在手机端无法跳转)
- [一、GFM TOC](#一gfm-toc)
        - [动机：](#动机)
  - [**1. Linux**](#1-linux)
    - [1.1 项目1：Github-Markdown-Toc](#11-项目1github-markdown-toc)
        - [1. 下载](#1-下载)
        - [2. 使用](#2-使用)
        - [3. 问题：](#3-问题)
    - [1.2 项目2：redcarpet](#12-项目2redcarpet)
    - [1.3 项目3：naokazuterada/MarkdownToc](#13-项目3naokazuteradamarkdowntoc)
    - [1.4 项目4：](#14-项目4)
    - [1.5 VSCode Markdown All in One (采用)](#15-vscode-markdown-all-in-one-采用)
        - [存在的问题：](#存在的问题)
  - [**2. Windows**](#2-windows)


# 零、概念和问题

## 概念

##### 1. GFM

- `Github Flavored Markdown`
- 是 Github 针对 Markdown 进行的一些扩展和改进。GFM 是 Github 用来解析和渲染 Markdown 文档的标准
  - README 文件、Issues、Pull Requests 等地方使用 GFM

## 问题

##### 1. 生成的目录在手机端无法跳转

经实验，只有纯英文的目录可调转

# 一、GFM TOC

##### 动机：

Github 中不会显示内容目录，阅读笔记较为繁琐，因此需要生成目录。

## **1. Linux**

### 1.1 项目1：Github-Markdown-Toc

使用开源项目 [github-markdown-toc](https://github.com/ekalinin/github-markdown-toc.git)

##### 1. 下载

```bash
wget https://raw.githubusercontent.com/ekalinin/github-markdown-toc/master/gh-md-toc
chmod a+x gh-md-toc
```

##### 2. 使用

1. 在需要生成目录的 `md` 文件对应位置添加如下两行：

   假设文件为 `test.md`

   ```markdown
   <!--ts-->
   <!--te-->
   ```

2. 运行

   ```bash
   ./gh-md-toc --insert test.md
   ```

3. 修改文加内容后，再次输入该命令后即可刷新目录。

##### 3. 问题：

与 github 不兼容

### 1.2 项目2：redcarpet

https://github.com/github/markup/issues/904



### 1.3 项目3：naokazuterada/MarkdownToc

https://github.com/naokazuterada/MarkdownTOC



### 1.4 项目4：

https://github.com/gtaifu/gfm_toc_generator



### 1.5 VSCode Markdown All in One (采用)

1. **安装插件**：
   - 打开 VS Code，点击左侧的扩展（Extensions）图标。
   - 搜索 `Markdown All in One` 并安装。
2. **生成目录**：
   - 打开你的 Markdown 文件。
   - 使用快捷键 `Ctrl+Shift+P` 打开命令面板。
   - 输入 `Create Table of Contents` 并选择它。
3. **插入目录**：
   - 插件会自动生成目录并插入到你的文件中。
4. **在 VSCode 中预览 Markdown 文件**
   - `ctrl + shift + v` 
5. **更新现有目录**
   - 在 typora 中更新 md 文加内容后
   - 打开 VSCode , 按 `ctrl + s` 保存文件，即可自动更新目录

##### 存在的问题：

若一级标题和二级标题的下一行是五级标题，五级标题的目录不会换行



## **2. Windows**

# 二、跳转到其他 markdown 文件
