# 一、使得 GitHub 正确显示 typora 自动生成的内容目录

## **1、Linux**

使用 Markdown 工具或插件来自动生成目录，然后将生成的目录插入到文档中。

例如，使用 `markdown-toc` 工具：

1. 安装 `markdown-toc`：

   ```bash
   npm install -g markdown-toc
   ```

2. 在你的 Markdown 文件中添加一个占位符，例如 `<!-- TOC -->`：

   ```markdown
   <!-- TOC -->
   
   ## Introduction
   ...
   ```

3. 运行 `markdown-toc` 工具生成目录：

   ```bash
   markdown-toc -i your-markdown-file.md
   ```

4. 这会自动在 `<!-- TOC -->` 位置插入生成的目录。

## **2、Windows**

1. - 在 Windows 上，有多种工具可以帮助你自动生成 Markdown 文件的目录（TOC）。以下是一些常用工具和方法：


### 1. Visual Studio Code 插件

Visual Studio Code（VS Code）有许多插件可以帮助你生成 Markdown 目录。下面是两款常用插件：

#### Markdown All in One

1. **安装插件**：
   - 打开 VS Code，点击左侧的扩展（Extensions）图标。
   - 搜索 `Markdown All in One` 并安装。

2. **生成目录**：
   - 打开你的 Markdown 文件。
   - 使用快捷键 `Ctrl+Shift+P` 打开命令面板。
   - 输入 `Create Table of Contents` 并选择它。

3. **插入目录**：
   - 插件会自动生成目录并插入到你的文件中。

#### Markdown TOC

1. **安装插件**：
   - 打开 VS Code，点击左侧的扩展（Extensions）图标。
   - 搜索 `Markdown TOC` 并安装。

2. **生成目录**：
   - 打开你的 Markdown 文件。
   - 使用快捷键 `Ctrl+Shift+P` 打开命令面板。
   - 输入 `Markdown TOC: Insert/Update` 并选择它。

3. **插入目录**：
   - 插件会自动生成目录并插入到你的文件中。

### 2. Typora

Typora 本身支持生成目录，你可以将目录直接复制粘贴到文件中：

1. **生成目录**：
   - 在 Typora 中打开你的 Markdown 文件。
   - 使用 `[TOC]` 插入目录。

2. **复制目录**：
   - 在 Typora 中将生成的目录复制。
   - 将复制的目录粘贴到适当的位置，然后保存文件。

### 3. 使用 Markdown 工具

如果你喜欢命令行工具，可以使用 `markdown-toc` 来生成目录。

1. **安装 Node.js**：
   - 下载并安装 [Node.js](https://nodejs.org/)。

2. **安装 markdown-toc**：
   - 打开命令提示符（Command Prompt）或 PowerShell。
   - 运行以下命令安装 `markdown-toc`：

     ```bash
     npm install -g markdown-toc
     ```

3. **生成目录**：
   - 在你的 Markdown 文件中添加占位符 `<!-- TOC -->`。
   - 运行以下命令生成目录：

     ```bash
     markdown-toc -i your-markdown-file.md
     ```

   - 这会在占位符位置插入生成的目录。

### 示例

假设你有一个 `README.md` 文件：

```markdown
# My Project

<!-- TOC -->

## Introduction
This is an introduction.

## Section One
This is section one.

### Subsection 1.1
This is subsection 1.1.

### Subsection 1.2
This is subsection 1.2.

## Section Two
This is section two.
```

运行 `markdown-toc` 命令后，文件会变成：

```markdown
# My Project

<!-- TOC -->
- [My Project](#my-project)
  - [Introduction](#introduction)
  - [Section One](#section-one)
    - [Subsection 1.1](#subsection-11)
    - [Subsection 1.2](#subsection-12)
  - [Section Two](#section-two)

## Introduction
This is an introduction.

## Section One
This is section one.

### Subsection 1.1
This is subsection 1.1.

### Subsection 1.2
This is subsection 1.2.

## Section Two
This is section two.
```

这样，生成的目录在 GitHub 上就可以正常显示。