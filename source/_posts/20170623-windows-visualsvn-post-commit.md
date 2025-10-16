---
title: Windows 下 VisualSVN 配置钩子（post-commit）
date: 2017-06-23 17:17:17
tags:
  - VisualSVN
categories: VisualSVN
---
在实际项目中，一般都要用到版本控制工具。这篇文章是关于版本控制工具 VisualSVN，对于网站（在线系统）类项目，我们在 `commit` 的同时，也希望新提交的代码能自动部署到服务器目录，便要用到钩子（hook）功能,具体配置的是 `post-commit`,如下图：

![](images/njbHBu2cK0.png)

在 windows server 服务器安装 VisualSVN server 服务端和 TortoiseSVN客户端，创建 repository，略过不提。

在项目名称点击鼠标右键，选择“Properties”->选择“Hooks”选项卡，选中 "Post-commit hook" ,点击 "Edit",编辑内容如下：

```
@echo off  
SET REPOS=%1  
SET REV=%2  
SET DIR=%REPOS%/hooks  
SET PATH=%PATH%;  
SET WORKING_COPY="C:/wamp64/www/test"
svn update %WORKING_COPY% --username username  --password password
```

**其中， WORKING_COPY 是项目在服务器（这里指如Apache、Nginx等web服务器）中的目录，并且是svn目录，这里要做的是：右键，签出（checkout）项目，而不是右键新建目录，这也是为何要安装TortoiseSVN的原因。**

这是，在自己的电脑上新建、修改文件，然后提交到版本库，可能出现如下错误：

```
post-commit hook failed (exit code 1) with output:
svn: E155004: Working copy 'C:\wamp64\www\test' locked
svn: E200031: sqlite: attempt to write a readonly database
svn: E200031: sqlite: attempt to write a readonly database
svn: run 'svn cleanup' to remove locks (type 'svn help cleanup' for details)
```

**原因：** Visual SVN Server服务的执行权限不够，不能对指定目录做读写操作。

**解决办法:**，修改Visual SVN Server 权限

     按 `win+R`快捷键，  在运行窗口中输入` services.msc`, 回车，在弹出的“服务”窗口中， 找到visual svn server 服务 ，右键属性 ，先停止服务之后，设置如下：



![](images/29192c5bCc.png)