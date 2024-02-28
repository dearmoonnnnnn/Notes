# 零、前言

chatgpt有三种注册/登录方式：使用邮箱、谷歌账号、微软账号

![image-20230414154120601](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414154120601.png)

chatgpt刚推出时，可以使用国内邮箱+国外手机号注册，由于现在注册时间较晚，该方案失效。并且，若谷歌/微软账号绑定的邮箱为国内邮箱，也无法注册。

本教程提供一种可行方案，可能不是最优解，仅供参考：

1. 通过虚拟手机号注册国外邮箱（gmail）
2. 使用第二个虚拟手机号+gmail，注册微软账号
3. 使用第三个虚拟手机号+微软账号，注册chatgpt

# 一、需要准备的东西

- 科学上网工具（本教程不提供）
- 国外虚拟手机号（网站：https://sms-activate.org/）
- gmail邮箱
- mirosoft账号

# 二、国外虚拟手机号

1、进入网站（最好挂vpn进），注册账号

![image-20230414151547128](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414151547128.png)

2、登录后点击右上角，充值（唯一需要花钱的地方）

![image-20230414151737165](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414151737165.png)

3、找到支付宝，充值金额选择最低的2美元，根据经验，完成chatgpt的注册完全够用。

==到此为止，手机号算是准备完成，以下步骤在需要短信验证码时再执行==

4、在左侧侧边栏"选择服务"栏目下搜索对应服务，如“OpenAI”，点击购物车即可购买

![image-20230414152946727](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414152946727.png)

![image-20230414153023098](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414153023098.png)

5、购买成功页面如下，这时可以使用该手机号接受短信验证码。

**把OpenAI输入框前面的国家改到你租的手机号的国家，就是OpenAI这边输入框后面的+xx 应该和你租的号码的+xx能对应的上。**

**输入框里输入的手机号就不要再带 +xx 的那个前缀了，括号内的数字要输入进去，括号不要输入进去，**

==注意，接受验证码可能失败，若主动点击×号或者20分钟内没有接受到验证码，将会自动退款，这时可以选择其他国家，重新购买。==

![image-20230414153130260](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414153130260.png)

# 三、注册gmail

https://accounts.google.com/signup/v2/createaccount?service=mail&continue=https%3A%2F%2Fmail.google.com%2Fmail%2F&flowName=GlifWebSignIn&flowEntry=SignUp

根据提示一步一步注册。gmail地址就是邮箱账号，选择自己容易记住的。

![image-20230414155301102](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414155301102.png)

需要手机号验证，参考步骤二后半部分，验证通过即注册完成。

**注意**：收不到验证码可以多试几个国家的手机号，这里推荐德国。

![image-20230414155429207](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20230414155429207.png)

# 四、注册mirosoft账号

[登录到您的帐户 (microsoftonline.com)](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?scope=service%3A%3Aaccount.microsoft.com%3A%3AMBI_SSL+openid+profile+offline_access&response_type=code&client_id=81feaced-5ddd-41e7-8bef-3e20a2689bb7&redirect_uri=https%3A%2F%2Faccount.microsoft.com%2Fauth%2Fcomplete-signin-oauth&client-request-id=bba47c60-1edd-4d64-b97f-25fe9cdfd79d&x-client-SKU=MSAL.Desktop&x-client-Ver=4.45.0.0&x-client-CPU=x64&x-client-OS=Windows+Server+2019+Datacenter&prompt=login&client_info=1&state=H4sIAAAAAAAEAAXBuWKCMAAA0H9xzcARBB06cMQUBIVySNxCBQwSuQP26_verp1xDxKolodMwCKVc71UDRDaeJ6lPfOD-PG8rVDzltVbCXqcA-tqoPMh5w0W37QK8oXUrWwmAhD_CMZiMqiYiuFl90-e353WmOBvHSLFVFOvb0BWgkEZqPbeJJJZHYFaL3VXLaYiYjxdrMMF4JHq_YcCO3iAU5srtVle1hfiCEaYo8C9RbFjjU3FEat1zyhlgkKFsZSZ8mnF6WbTBLhQ-RCdJVW8H80c6Y4lLX1THF-01Keai_QYDRz3g3aj5nB2o7N2nbvY0d0f6EhZeBeqOeK1Yj7ZBgzvSjQW81tUIzOEUXZeUoJcZuMf0V7z4HMWbQkN_amt7GptC7lbv752_3-upEdaAQAA&msaoauth2=true&lc=2052)

同上，根据提示，使用gmail和虚拟手机号创建账号。

这一步可以使用国内手机。chatgpt最终通过mirosoft账号登陆，梯子节点切换过频繁可能会导致账号异常，需要手机号验证。

# 五、注册chatgpt

这时会遇到各种各样的问题，90%的问题都可以通过如下方式解决：

1. 在浏览器设置中清楚所有cookie记录，重启浏览器
2. 浏览器使用无痕模式
3. 更换vpn节点，尽量不要选择亚洲节点，特别是香港节点！！能用欧美节点最好！

先把上述步骤执行一遍，然后再进入注册页面。

**重点：**

选择微软账户登录，一直到填写手机号这一步，根据步骤二，购买手机号，可能会出现两个问题：

1. 提示**Your account was flagged for potential abuse**
2. 跳转到验证码填写界面，但没有接收到短信验证码

这是手机号的问题，特别是问题1，由于注册时间较晚，网站大部分手机号都有该问题，这时只能大量尝试不同国家的手机号。