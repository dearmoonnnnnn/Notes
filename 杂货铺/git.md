# 零、概念与问题

## 概念

##### 1、工作目录（Working Directory)

工作目录是你正在进行编辑和修改文件的地方。

当你在工作目录中修改了文件，但还没有将这些修改保存到Git仓库时，这些修改就处于工作目录中。

##### 2、暂存区（Staging Area）

暂存区是用来暂时**存放**将要提交到Git仓库的**修改的地方**。

在执行`git add`命令后，修改的文件会被添加到暂存区。可以使用`git status`命令来查看暂存区中的文件变更情况。

- 为什么需要暂存区：

  暂存区的存在使得你可以将一系列的修改分成几个逻辑步骤提交，而不是一次性地提交所有的修改。这样可以更加灵活地控制你的提交，确保每次提交的内容都是有意义和逻辑完整的。

##### 3、本地仓库（Local Repository）

本地仓库是保存在你**本地计算机上的Git仓库副本**，包含了完整的项目历史记录。

当你执行`git commit`命令时，暂存区中的文件会被提交到本地仓库中。

## 问题

##### 1、在执行`git commit`时，为什么要用邮箱和用户名来验证身份信息，而不是用github的用户名和密码？

- Git 在进行提交时需要用户名和邮箱地址来记录提交者的身份信息，这是为了在提交历史中能够准确地追踪和识别每个提交的作者。这里的用户名和邮箱不需要与github用户名和邮箱一致，只是一个用于记录提交者身份的标识信息。

- GitHub 的用户名和密码用于访问 GitHub 服务器上的仓库，而不是在本地提交时的身份验证。在执行`git push`操作时，会需要用户名和密码进行鉴权。

##### 2、在执行`git push`时，需要github用户名和密码进行验证，已确保用户名和密码正确，但仍验证失败。

如果在执行 `git push` 时出现鉴权失败的情况，可能有几个原因导致：

1. **用户名或密码错误：** 首先确保你输入的 GitHub 用户名和密码是正确的。GitHub 账户的密码是大小写敏感的，因此请确保输入的密码与你的 GitHub 账户密码完全匹配。

2. **使用了双因素身份验证（2FA）：** 如果你的 GitHub 账户启用了双因素身份验证，你需要使用生成的一次性验证码（或应用程序生成的身份验证令牌）来完成身份验证。在这种情况下，你需要在密码后面输入一次性验证码。

   **关闭双因素身份验证：**

   1. 登录 GitHub 账户。
   2. 点击页面右上角的头像，然后选择 "Settings"（设置）。
   3. 在左侧边栏中，选择 "Passord and authentication"（安全）。
   4. 在 "Two-factor authentication"（双因素身份验证）部分，点击 "Edit"（编辑）。
   5. 输入你的 GitHub 密码以确认身份。
   6. 在 "Two-factor authentication" 页面中，选择 "Disable"（禁用），然后按照提示关闭双因素身份验证。

3. **访问权限问题：** 如果你正在尝试推送到一个你没有写权限的仓库，你将无法完成推送操作。请确保你有权限向目标仓库推送更改。

   **添加具有写权限的用户：**

   - 在仓库的设置页面点击`Collaborators`

   - 在`Manage access`中添加用户。
   - 但我是仓库的所有者，排除没有访问权限的错误。

4. **HTTPS 与 SSH 访问协议的问题：** 如果你使用 HTTPS 协议来访问 GitHub 仓库，并且你的仓库具有写权限，但是鉴权仍然失败，可能是由于本地 Git 配置问题或者 GitHub 服务端问题导致的。尝试更新你的 Git 凭据管理器（Credential Manager）或清除缓存，然后再次尝试。

5. **令牌（Token）鉴权问题：** 如果你使用了令牌（Personal Access Token）来代替密码进行身份验证，并且鉴权失败，可能是由于令牌配置错误或权限不足导致的。请确保你的令牌被正确地配置，并且拥有足够的权限。

查看bash的错误提示，可知GitHub 已经在2021年8月13日停止支持使用密码进行身份验证，而且建议使用其他方式进行身份验证，如个人访问令牌（Personal Access Token）。

![image-20240228150518139](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240228150518139.png)

##### 3、如何使用个人访问令牌（Personal）验证？

1. 在 GitHub 上生成个人访问令牌（Personal Access Token）。你可以在 GitHub 的设置页面中的 Developer settings > Personal access tokens 中生成令牌。

2. 生成令牌时，确保选择合适的权限，通常需要选择 repo 权限来访问仓库。

   - Fine-grained tokens和Tokens（classic）的区别
     - **Fine-grained tokens（细粒度令牌）：**
       - 细粒度令牌允许你为每个令牌选择精细的权限范围。
       - 你可以针对不同的操作或服务生成不同的细粒度令牌，以减少泄露令牌可能造成的风险。
       - 你可以通过选择具体的权限范围来限制令牌的访问权限，以保护你的账户和仓库的安全。
       - 细粒度令牌通常更安全，因为它们可以提供最小化的权限，仅允许令牌执行特定的操作。
     - **Tokens（classic）（传统令牌）：**
       - 传统令牌提供的权限范围相对较广，不像细粒度令牌那样可以选择具体的权限范围。
       - 传统令牌通常具有对仓库的读写访问权限，以及执行其他一些操作的权限。
       - 传统令牌的权限可能会更广泛一些，因此使用时需要更加谨慎，避免泄露令牌带来的潜在风险。

   这里直接选择传统令牌。

3. 复制生成的个人访问令牌。请注意，令牌只会显示一次，所以请确保保存好。

4. 在执行 `git push` 或 `git clone` 等操作时，将个人访问令牌作为密码输入。在提示输入密码时，输入你的 GitHub 用户名，然后将个人访问令牌粘贴到密码字段中。

5. 完成身份验证后，你应该能够成功进行操作。

# 下载特定分支

-b 

# 递归下载

##### 使用时机：

仓库中有`.gitmodules`文件，说明该仓库包含子模块，可以使用`--recursive`选项，递归地初始化和更新所有子模块。

##### 用法：

1. 克隆包含子模块的仓库

   ```bash
   git clone --recursive [repository_url]
   ```

   该命令会克隆主仓库并递归地初始化和更新所有子模块。

2. 初始化和更新子模块：

   如果已经克隆了一个包含子模块的仓库，但没有使用`--recursive`选项，可以使用以下命令初始化和更新子模块：

   ```bash
   git submodule update --init --recursive
   ```

##### `.gitmodules`文件

```ini
[submodule "submodule_name"]
    path = path/to/submodule
    url = https://github.com/example/submodule.git
```

- `submodule_name`：子模块的名称。
- `path`：子模块相对于主仓库的路径。
- `url`：子模块的远程仓库地址。

# 将本地项目添加到github仓库中

## 在linux中

##### 1、初始化本地仓库

在项目文件夹中初始化一个新的git仓库，用于管理项目文件夹

```bash
cd /path/to/your/project
git init
```

##### 2、将文件添加到暂存区

使用`git add`命令将项目中的文件添加到git的暂存区。使用`.`来添加所有文件，或者指定具体的文件名。

```bash
git add .
```

##### 3、提交更改

使用`git commit`命令将暂存区的文件提交到本地仓库，并添加一条提交消息。

```bash
git commit -m "Initial commit"
```

出现如下问题：

![image-20240228135216696](https://raw.githubusercontent.com/letMeEmoForAWhile/typoraImage/main/img/image-20240228135216696.png)

根据提示，运行

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

重新执行`git commit`

##### 4、关联远程仓库

在Github上创建一个新的仓库，并获得仓库的URL。然后使用`git remote add`命令将本地仓库与Github仓库关联起来。

```bash
git remote add origin https://github.com/username/repository.git
```

请将 URL 替换为实际的 GitHub 仓库地址。

##### 5、推送到远程仓库

使用`git push`命令将本地仓库的内容推送到Gtihub仓库。

```bash
git push -u origin master
```

如果使用了不同的分支名或者标签名，需要将`master`替换为实际的分支名或者标签名。

根据提示，输入github的用户名和密码进行身份验证。PS：这里的密码不能使用github登录时的密码。如果使用个人访问令牌验证，密码字段应使用生成的token.

