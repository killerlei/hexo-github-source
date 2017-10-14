---
title: hexo+github+AppVeyor实现不同电脑写博客
date: 2017-04-06 17:32:14
tags:  [hexo, AppVeyor, 持续集成]
categories: [front]
---
## hexo + github 部分
+ 以前一直用的像[博客园](http://www.cnblogs.com/)这样专业的博客网站,但是一直想有个个人站点,正好github提供这样的类似功能,并且发现hexo这样的好工具.hexo使用markdown写文章,并且支持github部署.这就很完美了.
+ 如何使用,网上已有好多教程,不再赘言.
+ 美中不足的是:换了电脑或者电脑坏了,源文件丢失,就得重新写,非常的麻烦.
+ 网上也有很多解决方式,我比较喜欢在github备份源文件,并且使用AppVeyor实现自动部署,不用在本地产生文件,也不用即备份又要部署那么繁琐.
+ 我是学习这篇教程做的,[Hexo的版本控制与持续集成](https://formulahendry.github.io/2016/12/04/hexo-ci/),以下也是我根据其做的实践.
<!-- more -->

## AppVeyor持续集成
我是在今年被问到CI,才知道持续集成这个东东的.也没有有机会实践,所以只能跟着教程一步一步操作.

1. 新建两个github仓库,一个是 [killerlei.github.io.](https://github.com/killerlei/killerlei.github.io)(以我的为例) ,另一个是便是备份源文件的仓库 [hexo-github-source](https://github.com/killerlei/hexo-github-source)(以我的为例).


2. 注册[APPVeyor](https://www.appveyor.com/),支持github登录,然后新建项目,直接选择github里面的源文件仓库[hexo-github-source](https://github.com/killerlei/hexo-github-source) 
![](http://oo0zdjapt.bkt.clouddn.com/hexo/imagesappveyor-p.png)

3. 在该项目的settings中设置Envirommemt
![](http://oo0zdjapt.bkt.clouddn.com/Appveyor-e.png)

4. 在源文件根目录中添加appveyor.yml配置文件,我的如下 
```
clone_depth: 5
environment:
  access_token:
    secure: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx(这里需要自己的)
install:
  - node --version
  - npm --version
  - npm install
  - npm install hexo-cli -g
build_script:
  - hexo generate
artifacts:
  - path: public
  on_success:
  - git config --global credential.helper store
  - ps: Add-Content "$env:USERPROFILE\.git-credentials" "https://$($env:access_token):x-oauth-basic@github.com`n"
  - git config --global user.email "%GIT_USER_EMAIL%"
  - git config --global user.name "%GIT_USER_NAME%"
  - git clone --depth 5 -q --branch=%TARGET_BRANCH% %STATIC_SITE_REPO% %TEMP%\static-site
  - cd %TEMP%\static-site
  - del * /f /q
  - for /d %%p IN (*) do rmdir "%%p" /s /q
  - SETLOCAL EnableDelayedExpansion & robocopy "%APPVEYOR_BUILD_FOLDER%\public" "%TEMP%\static-site" /e & IF !ERRORLEVEL! EQU 1 (exit 0) ELSE (IF !ERRORLEVEL! EQU 3 (exit 0) ELSE (exit 1))
  - git add -A
  - if "%APPVEYOR_REPO_BRANCH%"=="master" if not defined APPVEYOR_PULL_REQUEST_NUMBER (git diff --quiet --exit-code --cached || git commit -m "Update Static Site" && git push origin %TARGET_BRANCH% && appveyor AddMessage "Static Site Updated")
```
5. 第4步需要个人的secure,先到github新建 [Personal access tokens](https://github.com/settings/tokens),然后到AppVeyor加密[AppVeyor加密](https://ci.appveyor.com/tools/encrypt),然后写到第4步里

6. 现在就可以在本地写文章了,比如 <br>新建一篇文章  
>hexo new "hexo使用总结"<br> 
  hexo s   (在本地浏览器检查正常)<br>
  git push origin master (推送到源文件备份仓库)<br>

   现在AppVeor就开始自动构建.
![](http://oo0zdjapt.bkt.clouddn.com/hexo/images/appveyor-b1.png)
![](http://oo0zdjapt.bkt.clouddn.com/hexo/images/appveyor-b2.png) 

成功后就会把生成的文件推送到[killerlei.github.io.](https://github.com/killerlei/killerlei.github.io)仓库

![](http://oo0zdjapt.bkt.clouddn.com/hexo/images/git-io.png)    

就可以在[killerlei.github.io.](https://killerlei.github.io./)访问到新建的文章.

####  这样是不是很方便,换个电脑直接从源文件仓库clone下来,也不怕丢失.
ps:如果使用了hexo的非默认主题,可能会遇到这样的情况(比如我用的yilia主题):<br>
向源文件仓库push时,会失败,我在网上查了以下,好像是主题文件含有.git文件,本来受git控制,所以会冲突.需要删除.git. 我弄了好久才糊里糊涂弄好.如需帮助,请看[.git解决1](http://memory.blog.51cto.com/6054201/1217107)和[.git解决2](http://bbs.csdn.net/topics/390822726)
