typora的图片存储在本地，若把其他设备查看，必须同时传送图片，不方便。

采用图床的方法实现多设备查看图片。

# 零、Question

1. 能不能实现typora中删除图片时，同步删除图床中的图片
2. 导出为pdf时，图片不显示

# 一、相关概念

图床：存储图片的服务器，这里选择GitHub仓库，还可以使用smms

Pic-Core：上传图片的工具，这里使用命令行版本，只有在需要上传时才调用，不占用内存



# 二、github设置

## 1、创建仓库

创建新仓库，仓库类型一定要选为**Public**

![image-20230101162416097](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230101162416097.png)

## 2、生成token

2.1 点击设置

![image-20230101163506630](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230101163506630.png)

2.2 点击developer settings

![image-20230101164355977](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230101164355977.png)

2.3 选择`Tokens（classic）`，并点击 `Generate new token`创建token

![image-20230101164740620](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230101164740620.png)

2.4 时间设置为永不消失，权限设置：repo全部

![image-20230101170211300](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230101170211300.png)

2.5 点击 `generate token`，生成token。**token**只显示一遍，记得保存。

![generate_new_token](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/generate_new_token.jpg)

# 三、Pic-Core的下载与配置

依次打开文件、偏好设置、选择图像

插入图片时上传图片

下载PicGo-Core：在上传服务中选择 PicGo-Core（command line），点击下载或更新。可能需要翻墙。



![image-20230101171929907](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230101171929907.png)

下载完毕后打开配置文件，并输入相关配置信息

![image-20230103203145253](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230103203145253.png)

```
{
  "picBed": {
    "current": "github",
    "github": {
      "repo": "letMeEmoForAWhile/typoraImage", //自己的仓库名
      "branch": "main", //默认
      "token": "**************************", //github的token
      "path": "img/", //在仓库下再建一个img文件夹，可以为空
      "customUrl": "https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main" //按自己的来
    },
    "uploader": "github",
    "transformer": "path"
  },
  "picgoPlugins": {
    "picgo-plugin-github-plus": true
  }
}
```

# 四、遇到的问题及解决办法

github图床图片不显示，无法下载的问题

https://blog.csdn.net/qq_45172832/article/details/124698147

https://zhuanlan.zhihu.com/p/338289631

https://zhuanlan.zhihu.com/p/338289631

## 原因：

1. dns污染
2. host设置错误
3. 官方更新了dns，但是dns缓存没有被更新，导致解析错误

## 解决方法：

### step1：查询github及其二级域名IP地址

IP查询网站：

1. https://www.ipaddress.com/
2. https://www.ip138.com/

<img src="https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230103203953922.png" alt="image-20230103203953922" style="zoom:50%;" />

需要查询的域名：

- github.com
- assets-cdn.github.com
- raw.githubusercontent.com
- global.ssl.fastly.net/
- github.global.ssl.Fastly.net
- codeload.Github.com

### step2：修改本地host文件

host文件路径：C:\Windows\System32\drivers\etc\host

用记事本打开，并将查询到的IP地址填入

![image-20230103204347683](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230103204347683.png)