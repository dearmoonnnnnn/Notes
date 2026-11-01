# 需要的工具

1. GDB

   ```cmd
   sudo apt install gdb
   ```

   - Linux下调试C++ 的核心工具

2. 强烈推荐：Valgrind 和 Ctags

   ```cmd
   sudo apt install valgrind 
   sudo apt install exuberant-ctags
   ```

3. 基础编译依赖

   ```cmd
   sudo apt install build-essential cmake make bison flex pkg-config
   ```



# 编译指令

```cmd
make BUILD_TYPE=debug -j$(nproc)
```





# 编译

## 1. 找到可执行文件

```
find . -name vpr -type f -executable
```



## 2. 准备一次能跑通的命令









# 可视化页面调试