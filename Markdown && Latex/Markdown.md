# 零、概念

##### 1、GFM

- `Github Flavored Markdown`
- 是 Github 针对 Markdown 进行的一些扩展和改进。GFM 是 Github 用来解析和渲染 Markdown 文档的标准
  - README 文件、Issues、Pull Requests 等地方使用 GFM

# 一、生成 Github 中能显示的可跳转目录

##### 动机：

Github 中不会显示内容目录，阅读笔记较为繁琐，因此需要生成目录。

## **1、Linux**

### 项目1：Github-Markdown-Toc

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

### 项目2：redcarpet

https://github.com/github/markup/issues/904



### 项目3：naokazuterada/MarkdownToc

https://github.com/naokazuterada/MarkdownTOC

## **2、Windows**

