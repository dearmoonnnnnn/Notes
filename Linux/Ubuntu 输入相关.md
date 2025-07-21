# 一、问题描述：Ubuntu 16.04 无法输入中文

参考博客： [Ubuntu16.04LTS ibus pinyin 无法输入中文的处理办法_qianxuedegushi的博客-CSDN博客_ibus输入法打不出中文](https://blog.csdn.net/qianxuedegushi/article/details/103830782)

1. Ubuntu自带ibus输入法框架，若没有需要安装

   ```
   sudo apt-get install ibus ibus-clutter ibus-gtk ibus-gtk3 ibus-qt4
   ```

   启动ibus框架

   ```
   im-condig -s ibus
   ```

2. 安装拼音引擎

   ```
   sudo apt-get ibus-pinyin
   //sudo ibus-setup
   ```

   系统设置里的输入法选用IBus

   ![image-20221219225723721](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221219225723721.png)

3. 若有安装fcitx输入法，卸载

   ```
   sudo apt-get remove fcitx*
   sudo apt-get purge fcitx*
   ```

4. 在设置中的Text Entry中添加 Chinese(Pinyin)(IBus)

   ![image-20221219214853683](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221219214853683.png)

5. 若不行，重启Ubuntu

6. 可以正确输入，但是输入错乱

   在终端输入

   ```
   ibus-daemon -drx
   ```

7. 成功解决



仍存在的问题：在pycharm中无法正确使用

解决方法：pycharm中点击”help“，选择“Edit Custom VM Options”

![image-20221219214926041](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221219214926041.png)

在最后一行加入： -Drecreate.x11.input.method=true

![image-20221219214956210](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20221219214956210.png)

# 二、Ubuntu 20.04 一段时间后键盘卡死

- https://blog.csdn.net/m0_73640344/article/details/143751913?fromshare=blogdetail&sharetype=blogdetail&sharerId=143751913&sharerefer=PC&sharesource=qq_68844901&sharefrom=from_link
- https://blog.csdn.net/qq_68844901/article/details/147045701

##### 步骤 1: 创建并配置脚本    

在路径 `/home/your_username/scripts/restart_ibus.sh`，内容如下：

```bash
#!/bin/bash

ibus restart
```

- 若不存在该路径，使用 `mkdir` 创建

修改权限，使脚本具有可执行权限

```bsh
chmod +x /home/your_username/scripts/restart_ibus.sh
```



##### 步骤 2: 创建桌面快捷方式     

切换到桌面目录： 

```bash
cd /home/your_username/桌面/
```

创建 `.desktop` 文件：

```bash
gedit RestartIBus.desktop
```

输入内容：

```
[Desktop Entry]
Name=Restart_IBus
Comment=Double-click to run my script
Exec=/home/your_username/scripts/restart_ibus.sh
Icon=utilities-terminal
Terminal=true
Type=Application
Encoding=UTF-8
```

- Terminal ：是否弹出终端
- Exec : 可执行脚本路径

- Icon : 图标

修改权限

```bash
chmod +x RestartIBus.desktop
```

##### 步骤 3 ： 添加到应用菜单

拷贝 `.desktop` 文件到应用菜单路径

```bash
cp RestartIBus.desktop ~/.local/share/applications/
chmod +x ~/.local/share/applications/RestartIBus.desktop
```

在应用菜单里找到 `Restart_IBus`，添加到侧边栏
