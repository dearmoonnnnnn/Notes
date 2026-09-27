# find

## 基本用法

```bash
find /path/to/search -options
```

## 示例用法

##### 1、按文件名搜索

```bash
find /path/to/search -name "filename.txt"
```

上述命令在指定路径中搜索名为“filename.txt”的文件

找到文件名包含pre的文件：

- 方法1：

  ```bash
  find /path/to/search -type f -name "*pre*"
  ```

  - `/path/to/search`：需要搜索的目录路径。

  - `-type f`：指定只搜索文件，不包括目录。

  - `-name "*pre*"`：表示文件名中包含 "pre" 的文件。通配符 `*` 表示任意字符序列，所以 `*pre*` 表示文件名中包含 "pre" 的文件。

- 方法2：

  结合管道符`|`和`grep`实现同样的目标

  ```bash
  find /path/to/search -type f | grep pre
  ```

  - 先使用 `find` 命令搜索指定目录下的所有文件（不包括目录）
  - 然后将结果通过管道 `|` 传递给 `grep` 命令
  - `grep` 命令用来筛选包含 "pre" 的行（即文件名中包含 "pre" 的文件）。

##### 2、按类型搜索

```bash
find /path/to/search -type f
```

- 上述命令在指定路径中搜索文件，但不搜索目录

- 若仅搜索目录，将`-type f`改为`-type d`

# grep

## 用法：

用于在文本文件中搜索指定的文本模式，并将匹配到的行输出到标准输出（通常是终端）

```bash
grep [options] pattern [files]
```

- `[options]`
  - 可选参数，用于指定搜索的选项，如
    - `-i`：忽略大小写
    - `-r`：递归搜索
    - `-l`：只输出保安匹配文本的文件名
- `pattern`
  - 要搜索的文本模式，可以是普通字符串或正则表达式
- `files`
  - 要搜索的文件或者目录，可以指定一个或多个文件名或使用通配符匹配多个文件。

## 举例：

1. 在当前目录及子目录下查找包含“hello”文本的文件

   ```bash
   grep -rl "hello" .
   ```

   这会列出所有包含“hello”文本的文件名。

2. 在文件`example.txt`中搜索包含文本`hello`的行

   ```bash
   grep "hello" example.txt 
   ```


# chmod

更改文件权限



# echo

在终端中输出文本或变量的值

##### 重定向输出到文件

```bash
echo "This is a test." > output.txt
```

- 如果原文件已存在，会被覆盖

如果是追加文件

```bash
echo "Another line." >> output.txt
```



# free

##### 实时监控内存使用情况

```bash
watch -n 1 free -h
```

