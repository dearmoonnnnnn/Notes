# 零、概念

##### 1、GFM

- `Github Flavored Markdown`
- 是 Github 针对 Markdown 进行的一些扩展和改进。GFM 是 Github 用来解析和渲染 Markdown 文档的标准
  - README 文件、Issues、Pull Requests 等地方使用 GFM

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

##### 问题：

与 github 不兼容

### 1.2 项目2：redcarpet

https://github.com/github/markup/issues/904



### 1.3 项目3：naokazuterada/MarkdownToc

https://github.com/naokazuterada/MarkdownTOC



### 1.4 项目4：

https://github.com/gtaifu/gfm_toc_generator



### 1.5 VSCode Markdown All in One

1. **安装插件**：
   - 打开 VS Code，点击左侧的扩展（Extensions）图标。
   - 搜索 `Markdown All in One` 并安装。
2. **生成目录**：
   - 打开你的 Markdown 文件。
   - 使用快捷键 `Ctrl+Shift+P` 打开命令面板。
   - 输入 `Create Table of Contents` 并选择它。
3. **插入目录**：
   - 插件会自动生成目录并插入到你的文件中。

## **2、Windows**

