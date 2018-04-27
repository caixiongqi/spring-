# Git安装手册（Windows版）

## 安装git

1、下载git软件,下载地址：[https://git-scm.com/downloads](https://git-scm.com/downloads)

2、点击git.exe安装程序，点击`next`

3、点击`next`

4、根据自己的需要可以把需要安装软件（一般为`Git Bash`，`Git GUI`）选上，点击`next`

5、由于这是个人使用所以选择`第二项`

6、点击`next`,`finish`

## 配置环境变量

`控制面板` -> `系统和安全` ->  `系统` -> `环境变量` -> `系统变量`

1、新建系统变量，变量名为：`GIT_HOME` ，变量值为`git安装路径` ，如

`c:\Program Files (x86)\Git`

2、在系统变量`Path` 中加入`%GIT_HOME%\cmd`。注意：如果在Path的末尾加入环境变量，`不要加";"号`

3、点击`确定`

4、打开`cmd` 窗口输入git，测试环境变量是否正确

## 生成密匙，git账号配置密匙

这一步是为了可以通过git命令操作git库中的文件。

### 生成密匙

1、查看是否已经有了ssh密钥：`C:\Users\***\.ssh`。 如果没有密钥则不会有此文件夹，有则备份删除

2、在桌面上右击，点击`Git Bash`,然后输入`$ ssh-keygen -t rsa -C “xxx@163.com”`,按3个回车，密码不用输入，最后会提示密匙生成成功。

注：`xxx@163.com`为git账号中设置的email地址。

3、密钥生成后在`C:\Users\***\.ssh`中找到`.pub`文件，将文件内生成的密匙拷贝出来。

### git账号配置密匙

登录git账号，在账号设置中找到`SSH Keys` -> `Add SSH Key`,将生成的密匙拷贝到`Key`中，点击`Add key`即可。

## 域名IP映射，（非必需）

这一步是可以通过[https://git.s.com](https://git.s.com)访问git库。

在`C:\Windows\System32\drivers\etc\hosts`中加入配置`192.168.90.254 git.s.com`即可

## 登录账号后，项目不可见的处理

正常情况下，登录git后看不到已存在的`group`分组下的项目，这个时候需要`找有权限的用户`将你拉到此分组下。

将成员添加到分组步骤：

点击某一个`group`，`Members` -> `Add members`，在people中加入要添加的用户，在`Group Access`中授予权限，最后点击`Add users into group`即可。
