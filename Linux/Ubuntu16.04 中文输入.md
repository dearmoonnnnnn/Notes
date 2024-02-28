问题描述：Ubuntu 16.04 无法输入中文

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
