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

