# 一、安装与卸载

在Ubuntu 20.04上安装LaTeX发行版非常简单。你可以使用TeX Live，这是一个流行且功能强大的LaTeX发行版。以下是详细的安装步骤：

### 1. 更新软件包列表

首先，确保你的软件包列表是最新的：

```bash
sudo apt update
```

### 2. 安装TeX Live基本版

你可以安装基本版的TeX Live，这包含了大多数常见的LaTeX包和工具：

```bash
sudo apt install texlive
```

### 3. 安装TeX Live完全版（可选）

如果你需要更多的包和功能，可以安装TeX Live完全版。这会占用更多的磁盘空间，但几乎包含了所有可能需要的LaTeX包：

```bash
sudo apt install texlive-full
```

- 可能会卡住，多按几次回车

### 4. 安装额外的LaTeX工具（可选）

根据你的需求，你可能还需要一些额外的工具，如LaTeX编辑器和PDF查看器：

- 安装LaTeX编辑器TeXstudio：

  ```bash
  sudo apt install texstudio
  ```

- 安装PDF查看器Okular：

  ```bash
  sudo apt install okular
  ```

### 5. 验证安装

你可以通过编译一个简单的LaTeX文档来验证安装是否成功。创建一个名为 `test.tex` 的文件，并添加以下内容：

```latex
\documentclass{article}
\begin{document}
Hello, LaTeX!
\end{document}
```

然后在终端中运行以下命令来编译这个文件（根据文件第一行选择正确的编译引擎）：

```bash
pdflatex test.tex
```

or

```bash
xelatex test.tex
```

如果安装正确，应该会生成一个名为 `test.pdf` 的文件，你可以用PDF查看器打开它来查看输出。

### 6. 更新TeX Live（可选）

如果需要更新已安装的TeX Live包，可以使用以下命令：

```bash
sudo tlmgr update --self
sudo tlmgr update --all
```

请注意，`tlmgr` 是TeX Live包管理器，用于管理和更新TeX Live中的包。

以上步骤应该能帮助你在Ubuntu 20.04上成功安装和配置LaTeX发行版。

## 卸载:

如果你需要在Ubuntu上卸载`texlive-full`，可以使用以下步骤。这个过程将删除TeX Live的所有组件和相关数据。

### 卸载 `texlive-full`

1. **打开终端**。

2. **运行以下命令以卸载 `texlive-full` 包和它的依赖项**：

   ```bash
   sudo apt remove --purge texlive-full
   ```

   `--purge` 选项将删除包的所有配置文件。

3. **删除所有与TeX Live相关的包**：

   由于 `texlive-full` 包含很多子包，你可能需要运行以下命令来确保所有与TeX Live相关的包都被删除：

   ```bash
   sudo apt autoremove --purge texlive-*
   ```

   `autoremove` 选项将删除不再需要的自动安装的依赖项。

4. **清理缓存**：

   你还可以清理APT缓存来释放更多的空间：

   ```bash
   sudo apt clean
   ```

### 手动删除残留文件（可选）

即使使用了上面的命令，一些残留文件和目录可能仍然存在。你可以手动删除这些文件：

1. **删除TeX Live的安装目录**：

   默认情况下，TeX Live可能会安装到 `/usr/local/texlive` 或 `/usr/share/texlive` 目录。你可以运行以下命令来删除这些目录：

   ```bash
   sudo rm -rf /usr/local/texlive
   sudo rm -rf /usr/share/texlive
   ```

2. **删除用户级配置文件和缓存**：

   TeX Live可能会在你的主目录中创建一些配置文件和缓存。你可以运行以下命令来删除它们：

   ```bash
   rm -rf ~/.texlive*
   ```

### 验证卸载

最后，你可以运行以下命令来验证TeX Live是否已经完全卸载：

```bash
tex --version
```

如果系统返回 “command not found” 或类似的信息，说明TeX Live已成功卸载。

### 重新安装TeX Live（如果需要）

如果你想重新安装TeX Live，可以运行以下命令：

```bash
sudo apt update
sudo apt install texlive-full
```

通过这些步骤，你可以完全卸载TeX Live并清理相关文件和配置。



# 二、语法

```latex
\textnormal{}
```

当你在一个部分使用了特定的字体样式（如粗体、斜体等），可以用 `\textnormal` 恢复部分文本的默认样式。

# 三、Latex 自定义样式

