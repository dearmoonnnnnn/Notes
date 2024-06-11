# 零、问题、概念、错误

## 概念

##### 1、.sh文件

`.sh`文件是一个包含Shell脚本的文件。

- 通常由Shell语言编写，包括
  - `Bourne shell（sh）`
  - `Bourne Again shell（bash）`
  - `C shell（csh）`

## 问题

##### 1、shell脚本的应用场景？

1. 自动化任务
   - 备份、系统监控和维护、批量文件处理
2. 系统管理
   - 用户管理、服务管理、软件安装
3. 环境配置
   - 设置环境变量、启动应用程序
4. 快速原型开发
   - 快速测试和开发一些小工具和脚本

##### 2、语句后面是否要加分号

如果将多个命令写在一行，可以使用分号隔开

若一个命令写在一行，则不需要

##### 3、sh和bash的区别

bash兼容sh，并添加了新功能：

- 命令历史
  - 使用上下箭头
- 命令行编辑
  - 支持使用快捷键编辑命令行
- 数据结构增强
  - 增加了数组、关联数组、更复杂的参数扩展等特性
- 更强大的条件语句和循环结构
- `Shell`函数
  - 支持定义和调用函数

##### 4、等号前后什么时候加空格

赋值操作不能加

- 等号用于赋值操作，前后不能有空格。

- 如果在等号两边添加空格，Bash会将其解释为命令和参数，而不是赋值操作。

判断语句必须加

# 一、创建和运行shell脚本

##### 1、创建

使用任意文本编辑器创建，如

- `vim` 
- `gedit`

示例：

```bash
#!/bin/bash
# My first shell script
echo "Hello, World!"
```

- 确保开头包含 `#!/bin/bash`

##### 2、保存文件

将上述内容保存为`my_script.sh`

##### 3、在终端中，使用chmod命令赋予文件可执行权限

```bash
chmod +x my_scripyt.sh
```

##### 4、运行脚本

两种方法

- 直接运行

  ```bash
  ./my_script.sh
  ```

- 指定shell

  ```bash
  sh my_script.sh
  ```

  或

  ```bash
  bash my_script.sh
  ```


# 二、Bash编程的基本要素

## 1、Shebang

脚本文件的第一行通常为`\#!/bin/bash`，称为shebang

```sh
#!/bin/bash
```

- 告诉系统使用哪种解释器来执行脚本

## 2、变量

```sh
NAME="World"
echo "Hello, $NAME"
```

##### 命名规范

1. 大写变量名
   - 约定俗成，环境变量和全局变量使用全大写字母，如
   - `PATH`
   - `HOME`
2. 小写变量名
   - 局部变量、临时变量和自定义的变量名，如
   - `counter`
   - `filename`

## 3、注释

以`#`开头的行

```sh
# 这是一个注释
```

## 4、条件语句

`if`、`else`、`elif`

```bash
if [ "$NAME" = "World"]; then
	echo "Hello, World"
else
	echo "Hello, $NAME"
fi
```

- `fi`
  - 结束if条件语句的关键字

## 5、循环

包括`for`、`while`、`until`

```sh
for i in 1 2 3 ; do
	echo "Number: $i"
done
```

## 6、函数

定义和调用函数

```sh
my_function(){
	echo "Hello World"
}

my_function
```

## 7、脚本参数

通过`$1`、`$2`等访问脚本参数

```sh
echo "First argument: $1"
echo "Second argument: $2"
```

## 8、捕获命令输出

使用反引号conmmand或`$command`

```sh
DATE=$(date)
echo "Current date: $DATE"
```

或

```sh
DATE=`date`
echo "Current date: $DATE"
```

# 三、开机启动应用

## 1、使用`rc.local`(未成功)

将脚本添加到`/etc/rc.local`文件

1. 打开`/etc/rc.local`

   ```bash
   sudo vim /etc/rc.local
   ```

2. 添加要执行的脚本路径或者要启动的应用路径

   ```bash
   #!/bin/bash
   /path/to/your/script.sh
   /usr/local/sunlogin/bin/sunloginclient
   exit 0
   ```

3. 改变权限

   ```bash
   sudo chmod +x /etc/rc.local
   ```

   