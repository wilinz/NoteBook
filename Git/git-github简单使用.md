# Git简单使用

Create by [https://github.com/wilinz](https://github.com/wilinz)

## 建议

修改代码后每天提交一次！！！

修改代码后每天提交一次！！！

修改代码后每天提交一次！！！

## 前提条件

![image-20231125203101675](https://raw.githubusercontent.com/wilinz/picgo/main/image/202311252031761.png)

## 下载Git

##  [https://git-scm.com/downloads](https://git-scm.com/downloads)

1. 选择操作系统，如 Windows
2. 选择 Standalone Installer
3. 点击 64-bit Git for Windows Setup
4. 安装，一路 Next

新下载的Git，需要配置邮箱和用户名

```shell
git config --global user.email "你的邮箱"
git config --global user.name "你的用户名"
```

## 上传

### 创建 Github 仓库
1. Github 创建新仓库
2. 拷贝Git链接

### 初次上传

```shell
  # 这是第一次上传
  git init # 初始化并建仓库
  git add . #添加当前目录所有文件到仓库, " add ." 其中"."表示当前目录下所有文件
  git commit -m "message" #  提交代码，message 是对本次提交的一次描述，最好修改一下
  git branch -M main #设置主要分支为main, main是分支名字 
  git remote add origin https://github.com/xxx/xxx.git # 添加远程仓库地址，替换为你的仓库地址
  git push -u origin main #传到github等远程仓库，并指定远程分支为main分支
```

### 后续上传

#### 使用命令行

```shell
  # 后续上传，可以使用命令，也可以使用 IDE 集成的工具（推荐）
  git add . #添加当前目录所有文件到仓库
  git commit -m "message" #  提交代码，message 是对本次提交的一次描述，最好修改一下
  git pull -r # 可选，如果在github上修改了代码，需要执行这个pull同步github代码到本地
  git push #传到github等远程仓库
```

#### 使用 IDE 集成的工具（推荐）

![202311251956977](https://raw.githubusercontent.com/wilinz/picgo/main/image/202311252010173.png)

代码提交三部曲：

```shell
git add . 
git commit -m "message"
git push # 如果失败先运行 git pull -r
```

## 建议

修改代码后每天提交一次！！！

修改代码后每天提交一次！！！

修改代码后每天提交一次！！！

## 如何查看 Git 提交历史

![image-20231125201421309](https://raw.githubusercontent.com/wilinz/picgo/main/image/202311252014403.png)

## 小技巧

记不住命令怎么办

1. Github 创建仓库

2. 拷贝Git链接

   ![](https://raw.githubusercontent.com/wilinz/picgo/main/image/image-20231125201653899.png)

3. 克隆仓库

   ![image-20231125201848384](https://raw.githubusercontent.com/wilinz/picgo/main/image/202311252018473.png)

4. 跳出文件管理器后，在文件管理器空白处右键单击 -> 在此终端中打开 -> 运行下面命令

   ```shell
   git clone https://github.com/xxx/example_repository.git
   ```

5. 进入克隆下来的目录 example_repository , 将 .git 文件夹拷贝到外层目录，然后删除 example_repository 目录（example_repository 是你的 Github 仓库名）
6. 成功，现在开始，可以使用 IDE 操作了

![202311251956977](https://raw.githubusercontent.com/wilinz/picgo/main/image/202311252010173.png)

## 建议

修改代码后每天提交一次！！！

修改代码后每天提交一次！！！

修改代码后每天提交一次！！！