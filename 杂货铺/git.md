- [零、概念与问题](#零概念与问题)
  - [概念](#概念)
        - [1、工作目录（Working Directory)](#1工作目录working-directory)
        - [2、暂存区（Staging Area）](#2暂存区staging-area)
        - [3、本地仓库（Local Repository）](#3本地仓库local-repository)
  - [问题](#问题)
        - [1、在执行`git commit`时，为什么要用邮箱和用户名来验证身份信息，而不是用github的用户名和密码？](#1在执行git-commit时为什么要用邮箱和用户名来验证身份信息而不是用github的用户名和密码)
        - [2、在执行`git push`时，需要github用户名和密码进行验证，已确保用户名和密码正确，但仍验证失败。](#2在执行git-push时需要github用户名和密码进行验证已确保用户名和密码正确但仍验证失败)
        - [3、如何使用个人访问令牌（Personal）验证？](#3如何使用个人访问令牌personal验证)
  - [常见错误](#常见错误)
        - [1、无法推送一些引用到...](#1无法推送一些引用到)
        - [2、git add 特定文件夹出现报错](#2git-add-特定文件夹出现报错)
        - [3、error: 源引用规格 main 没有匹配 error: 无法推送一些引用到 'origin'](#3error-源引用规格-main-没有匹配-error-无法推送一些引用到-origin)
        - [4、Github 无法正确显示 latex 公式](#4github-无法正确显示-latex-公式)
- [一、git clone用法](#一git-clone用法)
  - [1、下载特定分支](#1下载特定分支)
  - [2、递归下载](#2递归下载)
        - [动机：](#动机)
            - [用法：](#用法)
            - [`.gitmodules`文件](#gitmodules文件)
- [二、将本地项目添加到github仓库中](#二将本地项目添加到github仓库中)
  - [A、linux操作系统](#alinux操作系统)
        - [1、初始化本地仓库](#1初始化本地仓库)
        - [2、将文件添加到暂存区](#2将文件添加到暂存区)
        - [3、提交更改](#3提交更改)
        - [4、关联远程仓库](#4关联远程仓库)
        - [5、推送到远程仓库](#5推送到远程仓库)
        - [github创建空仓库后的提示：](#github创建空仓库后的提示)
        - [create a new repository on the command line：](#create-a-new-repository-on-the-command-line)
        - [push an existing repository from the command line](#push-an-existing-repository-from-the-command-line)
        - [本地项目已经与远程仓库关联，当本地项目产生部分更新时，如何推送到github](#本地项目已经与远程仓库关联当本地项目产生部分更新时如何推送到github)
  - [B、windows操作系统](#bwindows操作系统)
- [三、git push/pull 特定文件夹或者文件](#三git-pushpull-特定文件夹或者文件)
        - [方法1：使用`sparse-checkout：`（未实践）](#方法1使用sparse-checkout未实践)
            - [方法2：使用`.gitignore`文件](#方法2使用gitignore文件)


# 零、概念与问题

##  概念

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

##  问题

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

##  常见错误

##### 1、无法推送一些引用到...

```bash
 ! [rejected]        main -> main (fetch first)
error: 无法推送一些引用到 'https://github.com/letMeEmoForAWhile/Notes.git'
提示：更新被拒绝，因为您当前分支的最新提交落后于其对应的远程分支。
提示：再次推送前，先与远程变更合并（如 'git pull ...'）。详见
提示：'git push --help' 中的 'Note about fast-forwards' 小节。
```

**错误原因**：

尝试推送到远程仓库时，远程仓库已经包含本地仓库中不存在的提交，或者远程仓库的历史记录领先于本地仓库。

**解决方法**：

1. **拉取远程仓库的更新：** 在推送之前，先使用 `git pull` 命令将远程仓库的更新拉取到本地：

   ```bash
   git pull origin main
   ```

   如果你的默认分支是 `main`，则将 `main` 替换为你的默认分支名称。

   这会将远程仓库的更新合并到你的本地分支。如果有冲突发生，需要解决冲突并提交合并结果，见下方“解决冲突的步骤”

2. **推送你的更改到远程仓库：** 一旦你的本地分支与远程分支同步了，再次尝试推送你的更改：

   ```bash
   git push origin main
   ```

   这将会将你的本地更改推送到远程仓库的 `main` 分支中。

**git pull 冲突举例：**

如果远程仓库的更新是删除文件A，而本地项目的更新是在文件A中添加了更多的文本，那么在拉取远程更新时会发生以下情况：

1. Git 尝试将远程更新合并到你的本地分支中。
2. Git 发现远程更新删除了文件A，但你的本地分支中有对文件A的修改。
3. 这种情况下，Git 会将文件A标记为 "deleted by us"，表示远程仓库删除了该文件，但你的本地分支对其有修改。
4. 由于存在冲突，Git 不会自动解决，而是将冲突标记在文件A中，需要你手动解决。

**以下是解决冲突的具体步骤：**

1. 打开包含冲突的文件A，并编辑文件以解决冲突。通常，冲突区域会被特殊标记，类似于以下内容：

    ```plaintext
    <<<<<<< HEAD
    你的本地更改
    =======
    远程仓库的更改
    >>>>>>> branch-name
    ```

    你需要决定保留哪些更改。根据你的需求修改冲突区域，删除 Git 添加的冲突标记（`<<<<<<< HEAD`、`=======`、`>>>>>>> branch-name`）。

2. 保存文件A，并关闭编辑器。

3. 在终端中，使用 `git add` 命令将解决了冲突的文件A标记为已解决。假设文件A已经在工作区中：

    ```bash
    git add A
    ```

    如果有多个冲突文件，可以将它们一次性添加：

    ```bash
    git add A B C
    ```

    其中 `A`、`B`、`C` 是冲突的文件名。

4. 提交解决冲突的修改。添加一个描述解决冲突的提交消息：

    ```bash
    git commit -m "Resolve conflict in file A"
    ```

    如果你愿意，你可以提供更具体的描述来说明你解决了什么冲突。

5. 最后，将你的更改推送到远程仓库：

    ```bash
    git push origin main
    ```

    如果默认分支不是 `main`，将 `main` 替换为默认分支名称。

这样，你就成功解决了冲突，并将更改推送到了远程仓库。

##### 2、git add 特定文件夹出现报错

```bash
尚未暂存以备提交的变更：
  （使用 "git add/rm <文件>..." 更新要提交的内容）
  （使用 "git restore <文件>..." 丢弃工作区的改动）


未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）
	.vscode/c_cpp_properties.json
	build/cloud_merging/
	devel/lib/cloud_merging/
	devel/lib/pkgconfig/cloud_merging.pc
	devel/share/cloud_merging/

```

不想跟踪上述的未跟踪文件。

创建`.gitignore`文件，在其中添加想要忽略的文件或文件夹的路径。例如：

```plaintext
.vscode/
build/
devel/
```

##### 3、error: 源引用规格 main 没有匹配 error: 无法推送一些引用到 'origin'

**错误原因**：

本地分支与远程仓库的分支名称不匹配。

使用

```bash
git branch -M [仓库名]
```

修改本地分支名称

##### 4、Github 无法正确显示 latex 公式

渲染成网页，暂未实现

##### 5、未提交 commit 到当前分支，创建了新的分支导致当前分支 commit 丢失。

在本地修改文件后没有提交到 `main`，而是在新分支中提交了所有的更改，导致 `main` 中未保存这些修改。

解决方案1: 合并新分支与当前分支

1. 切换到 main 分支

   ```
   git checkout main
   ```

2. 合并新分支的更改

   ```bash
   git merge new-branch
   ```

   

# 一、git clone 用法

##  1、下载特定分支

```bash
git clone -b branch_name repository_url
```

- `branch_name` 是你想要克隆的分支的名称。

- `repository_url` 是你要克隆的远程仓库的 URL

举例：

```bash
git clone -b development https://github.com/username/repository.git
```

##  2、递归下载

##### 动机：

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

##### `.gitmodules` 文件

```ini
[submodule "submodule_name"]
    path = path/to/submodule
    url = https://github.com/example/submodule.git
```

- `submodule_name`：子模块的名称。
- `path`：子模块相对于主仓库的路径。
- `url`：子模块的远程仓库地址。

# 二、将本地项目添加到 github 仓库中

##  A、linux 操作系统

##### 1、初始化本地仓库

在项目文件夹中初始化一个新的 git 仓库，用于管理项目文件夹

```bash
cd /path/to/your/project
git init
```

##### 2、将文件添加到暂存区

使用 `git add` 命令将项目中的文件添加到 git 的暂存区。使用`.`来添加所有文件，或者指定具体的文件名。

```bash
git add .
```

##### 3、提交更改

使用 `git commit` 命令将暂存区的文件提交到本地仓库，并添加一条提交消息。

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

重新执行 `git commit`

##### 4、关联远程仓库

在 Github 上创建一个新的仓库，并获得仓库的 URL 。然后使用 `git remote add` 命令将本地仓库与 Github 仓库关联起来。

```bash
git remote add origin https://github.com/username/repository.git
```

请将 URL 替换为实际的 GitHub 仓库地址。

##### 5、推送到远程仓库

使用 `git push` 命令将本地仓库的内容推送到 `Gtihub` 仓库。

```bash
git push -u origin master
```

- `-u` 参数

  - `-u` 选项用于将本地分支与远程仓库的分支进行关联。

  - 已经执行过一次推送，并且已经建立了本地分支与远程分支的关联，那么在下次推送时，你可以直接使用 `git push` 命令：

    ```bash
    git push origin master
    ```

- **推送到 master 以外的分支：**

  如果使用了不同的分支名或者标签名，需要将 `master` 替换为实际的分支名或者标签名，并且需要重命名当前所在分支。

  例如：如果是推送到 `main` 分支。

  先重命名本地仓库的当前分支为 `main`

  ```bash
  git branch -M main
  ```

  再推送到远程仓库的 `main` 分支

  ```bash
  git push -u origin main
  ```

- **身份验证：**

  根据提示，输入 `github` 的用户名和密码进行身份验证。PS：这里的密码不能使用 `github` 登录时的密码。如果使用个人访问令牌验证，密码字段应使用生成的 `token`.


##### github 创建空仓库后的提示：

##### create a new repository on the command line：

```bash
echo "# RosDriverForARS548" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/letMeEmoForAWhile/RosDriverForARS548.git
git push -u origin main
```

##### push an existing repository from the command line

```bash
git remote add origin https://github.com/letMeEmoForAWhile/RosDriverForARS548.git
git branch -M main
git push -u origin main
```

##### 本地项目已经与远程仓库关联，当本地项目产生部分更新时，如何推送到github

只需要执行2、3、5步骤

##  B、windows 操作系统

使用 GithubDesktop 客户端

# 三、git push/pull 特定文件夹或者文件

##### 方法1：使用 `sparse-checkout：`（未实践）

https://blog.csdn.net/yuyefuxiao521/article/details/132292012

##### 方法2：使用 `.gitignore` 文件

1、在根目录下创建 `.gitignore` 文件

2、例如，忽略所有 `.log` 文件和名为 `temp` 的文件夹，文件应该包含以下内容：

```txt
*.log
temp/
```

- 对所有层级的 `temp/` 文件夹和 `*.log` 都生效
- windows 和 Linux 中都用 `/`
- 也可以直接写 `temp`

3、保存并关闭

4、使用以下命令将 `.gitignore` 文件添加到版本控制中

```bash
git add .gitignore
git commit -m "Add .gitignore file"
```

# 四、将 clone 的项目发布到新仓库

## 1. 创建一个新仓库

在 Github 中创建新的仓库

## 2. 断开原仓库的链接

##### 2.1 检查当前远程仓库

```bash
git remote -v
```

可能输出

```plaintext
origin  https://github.com/other-user/original-repo.git (fetch)
origin  https://github.com/other-user/original-repo.git (push)
```

##### 2.2 删除原远程仓库链接

```bash
git remote remove origin 
```

##### 2.3 绑定到新的 Github 仓库

```bash
git remote add origin main https://github.com/your-username/my-project.git
```

##### 2.4 推送

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

# 五、撤销提交

##### 动机

`git commit -m` 提交了错误信息

### 场景 1: 提交后尚未推送到远程仓库

如果你只是本地错误提交，可以直接撤销上一次提交：

#### 方法 1: **撤销提交并保留改动（推荐）**

使用以下命令撤销上一次提交，同时保留修改内容在暂存区（staging area），以便重新提交：

```bash
git reset --soft HEAD~1
```

- `HEAD~1` 表示回退到上一个提交。
- 你的修改会保留在暂存区，可以通过 `git commit -m "正确的提交信息"` 重新提交。

#### 方法 2: **撤销提交并清除暂存区**

如果你想撤销提交并将更改移回工作区（unstaged），可以使用：

```bash
git reset HEAD~1
```

- 这样更改会被移到工作区，而不再在暂存区中。

#### 方法 3: **完全撤销提交和改动**

如果你希望删除提交和对应的更改（不可恢复），可以执行：

```bash
git reset --hard HEAD~1
```

- 请谨慎使用此命令，因为未保存的修改会被丢弃。

------

### 场景 2: 提交后已推送到远程仓库

如果错误的提交已经被推送到远程仓库，你需要同步修改远程记录。

#### 方法 1: **修改提交记录**

如果只需要修正提交信息，可以使用以下命令修改上一次提交：

```bash
git commit --amend -m "正确的提交信息"
```

然后强制推送到远程仓库：

```bash
git push --force
```

#### 方法 2: **回滚提交（需要强制推送）**

如果提交本身是错误的，需要撤销，可以先回退本地分支，再推送到远程：

```bash
git reset --soft HEAD~1
git push --force
```

- **注意**：强制推送（`--force`）可能会覆盖远程的历史记录，请确保与团队成员沟通，避免造成混乱。

------

### 场景 3: 仅提交信息错误，但内容正确

如果只是提交信息有误，而提交的内容正确，可以直接使用以下命令修改提交信息：

```bash
git commit --amend -m "正确的提交信息"
```

- 这会替换上一次提交的信息。

如果已推送到远程，请记得强制推送：

```bash
git push --force
```

------

### 总结

- **撤销本地提交**：使用 `git reset`，根据需要选择 `--soft`（保留暂存区）或 `--hard`（完全丢弃改动）。
- **修改提交信息**：使用 `git commit --amend`。
- **已推送时**：需要强制推送（`--force`）更新远程记录，谨慎操作。
