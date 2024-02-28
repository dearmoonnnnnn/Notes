# ERROR1：Cannot uninstall‘xx’.It is a distutils installed project

**问题描述：**

![image-20221219204400692](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221219204400692.png)

需要安装指定版本的包，但是电脑中已存在另一版本的包且无法卸载.



**解决方法:**



```
pip install --ignore-installed [package]
```

![image-20221219204314419](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221219204314419.png)

# 使用国内镜像源

## 1、临时使用

```
pip install [package] -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 2、永久使用

### 2.1 命令行方式

`-i` 的方式只是临时指定下载的源，还可以更改默认设置永久修改默认下载源。

```csharp
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 2.2 文件方式

Linux/Mac os 环境中，配置文件位置在 ~/.pip/pip.conf（如果不存在创建该目录和文件）

```
mkdir ~/.pip
```

打开配置文件 **~/.pip/pip.conf**，修改如下：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```

查看 镜像地址：

```
$ pip3 config list   
global.index-url='https://pypi.tuna.tsinghua.edu.cn/simple'
install.trusted-host='https://pypi.tuna.tsinghua.edu.cn'
```

Windows下，你需要在当前对用户目录下（C:\Users\xx\AppData\Roaming\pip\，xx 表示当前使用对用户，比如张三）创建一个 pip.ini在pip.ini文件中输入以下内容：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```

## 3、国内镜像源列表

清华大学：[https://pypi.tuna.tsinghua.edu.cn/simple](https://links.jianshu.com/go?to=https%3A%2F%2Fpypi.tuna.tsinghua.edu.cn%2Fsimple)
中国科技大学 [https://pypi.mirrors.ustc.edu.cn/simple/](https://links.jianshu.com/go?to=https%3A%2F%2Fpypi.mirrors.ustc.edu.cn%2Fsimple%2F)
阿里云：[http://mirrors.aliyun.com/pypi/simple/](https://links.jianshu.com/go?to=http%3A%2F%2Fmirrors.aliyun.com%2Fpypi%2Fsimple%2F)
腾讯云：[https://mirrors.cloud.tencent.com/pypi/simple](https://links.jianshu.com/go?to=https%3A%2F%2Fmirrors.cloud.tencent.com%2Fpypi%2Fsimple)
华为云：[https://repo.huaweicloud.com/repository/pypi/simple](https://links.jianshu.com/go?to=https%3A%2F%2Frepo.huaweicloud.com%2Frepository%2Fpypi%2Fsimple)
豆瓣：[http://pypi.douban.com/simple/](https://links.jianshu.com/go?to=http%3A%2F%2Fpypi.douban.com%2Fsimple%2F)
网易：[http://mirrors.163.com](https://links.jianshu.com/go?to=http%3A%2F%2Fmirrors.163.com)
