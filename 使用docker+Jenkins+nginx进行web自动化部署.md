> 前段时间无意间翻看某个大神的QQ个人说明，有句话突然点醒了迷途中的我 —— 知道 + 做到 = 得到。这句话在我当下的状况显得特别的符合语境，学习某个知识或者解决某个问题的时候，习惯性的总是通过大量的搜索来扩展自己的知识面，但是我发现，能够搜出来百分之50以上的东西，知识的重复性非常的大，因此萌生了做一些专题的知识性总结。这篇文章的目的是搭建前端项目的自动化部署，其中涉及到 docker 的安装部署，jenkins 和 nginx的相关配置。其中个人学习用来学习的成本大概是100块左右（服务器+域名）

## 服务器环境
  服务器：腾讯云主机（1 核 1 GB 1 Mbps） Ubuntu 16.04.1，域名：hookyun.cn，远程连接工具：mobaxterm

  服务器已有环境：nodejs v10.14.1，npm 6.5.0，nginx：1.10.3

## docker安装
  参考教程：[docker安装](https://yeasy.gitbooks.io/docker_practice/install/ubuntu.html)
  在安装前务必要检查下系统是否已经装了docker，可以使用 docker version 进行检查，安装成功会有如下相关信息：
  ![image](https://img-blog.csdnimg.cn/2019010616512579.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
  如果在安装的过程中出现了 Warning: the "docker" command appears to already exist on this system 的失败信息，则表示系统可能已经安装了docker。 
  以下docker相关命令接下来可能会涉及：  
```
//查看所有的docker容器：
    docker container ls -a
//终止容器：
    docker container stop
//启动容器：
    docker container start
//重启容器：
    docker container restart
//删除容器：
    docker container rm
```
## Jenkins安装
### 1、拉取docker jenkins镜像
```
docker pull jenkins:latest
```
### 2、配置jenkins相关目录，并给予对应的权限
```
mkdir -p /var/jenkins_home
chown -R 1000  /var/jenkins_home
```
实践中考虑在哪个目录下新建配置目录，可以参考Linux的目录结构
![image](https://img-blog.csdnimg.cn/20190106171408713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
### 3、启动docker 
-d 代表后台运行 -p 指的是映射容器端口与服务器端口 --name 指对镜像所自定义的名称 ，-v 指的是自定义配置jenkins目录，最后的参数jenkins指的是使用的是本地的jenkins镜像
```
docker run -d -p 49002:8080 --name myjenkins -v /var/jenkins_home:/var/jenkins_home jenkins
```
启动成功后可以 docker container ls -a 来查看jenkins的状态，同时在对应的配置目录下可以看到很多生成的文件
![image](https://img-blog.csdnimg.cn/20190106172824781.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
### 4、初始化Jenkins
浏览器访问 http://94.191.33.76:49002，会出现如下界面，根据提示，到对应的目录下/var/jenkins_home/secrets/initialAdminPassword找到秘钥后在输入框内输入 
![image](https://img-blog.csdnimg.cn/20190106190413693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
### 5、选择第一个安装配置
![image](https://img-blog.csdnimg.cn/20190106191311595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
进入安装界面，这里需要等待比较久，实际安装过程中，可能是网络比较慢的原因，出现部分插件安装失败，不要慌，这里暂时忽略，因为不影响后续的流程。
![image](https://img-blog.csdnimg.cn/20190106191441989.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
### 6、配置Jenkins的admin用户
![image](https://img-blog.csdnimg.cn/20190106191857526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
至此，Jenkins已经安装完毕，接下来就是前端自动化部署的相关配置操作了。
![image](https://img-blog.csdnimg.cn/20190106193737714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
## Jenkins前端自动化构建配置
  在一般的前端开发流程中，我们先在本地完成前端项目的开发工作，接着将编译 （npm run build ）好后的（dist）文件通过远程传输工具（mobaxterm）传输到服务器上，同时我们将本地的代码push到git上，在这样的流程中，都是通过人工（命令行）的方式去执行，这样流程每次都会有一些重复性 （编译 部署） 的东西，因此我们可以通过配置jenkins的相关插件，从而构建这样一套自动化部署流程，简化开发流程。一般的流程中，我们会有两台服务器，一台是进行自动化构建的Jenkins服务器，另外一台式用于存放项目的正式服务器。在此次实践中，我只用了同一台服务器同时作为构建以及正式服的发布用。
### Jenkins插件安装
安装 Publish Over SSH 、Git plugin 、NodeJS Plugin
1、首页，点击系统管理 —> 管理插件
![image](https://img-blog.csdnimg.cn/20190106194221626.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
2、可选插件 —> 右上角过滤SSH —> 选择 Publish Over SSH —>  点击 直接安装
![image](https://img-blog.csdnimg.cn/20190106194148588.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
3、同理 Git plugin 和 NodeJS Plugin 都是相同的安装步骤，安装完毕后可以在已安装列表中看到对应的插件
![image](https://img-blog.csdnimg.cn/2019010619490438.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
### Jenkins插件配置
1、配置 Publish Over SSH 
点击 —> 系统管理 —> 系统设置，找到 Publish over SSH
![image](https://img-blog.csdnimg.cn/20190106201646914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
- Passphrase： 密码（目标机器的密码）
- Path to key：key文件（私钥）的路径
- SSH Server Name： 服务器标识名字
- Hostname： 需要连接ssh的主机名或ip地址，此处填写应用服务器IP（建议ip）
- Username： 用户名
- Remote Directory： 远程目录(要发布的目录)

这里可以采用秘钥的形式，也可以直接输入目标机器密码的形式进行连接，这里采用直接输入密码的形式（比较简单方便）
2、配置nodejs版本
点击 —> 系统管理 —> Global Tool Configuration，找到 NodeJS 配置 ，这里安装了 11.6.0和 10.14.0 两个版本（根据项目自行调整），保存配置。
![image](https://img-blog.csdnimg.cn/20190106202358246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
## 构建前端项目
这里选择vue-cli 搭建一个helloworld 的前端项目，目录结构如下：
![image](https://img-blog.csdnimg.cn/20190106204723576.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
前端界面如下：
![image](https://img-blog.csdnimg.cn/20190106204837682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
## 创建自动化部署任务
1、点击左侧 — 新建
![image](https://img-blog.csdnimg.cn/20190106203048979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
2、源码管理
这里我们通过vue-cli构建一个项目，并传到码云上，由于是私有项目，所以我们要添加一下Git账号
![image](https://img-blog.csdnimg.cn/20190106203531979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
3、配置构建环境，选择node版本
![image](https://img-blog.csdnimg.cn/20190106203953212.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
4、配置构建脚本
rm -rf dist 移除原有dist文件 ，npm run build 执行编译命令，tar -zcvf dist.tar.gz * 压缩编译后的文件。
需要注意的是，我这里没有加入npm install 的执行操作，因为我的Linux主机本来配置就非常低，加入npm install的话会导致整个Linux主机直接陷入卡死，我在第一次实践的时候尝试加入了npm install的操作，结果整个机器卡死，最后只能重启机器来进行恢复。所以这里我通过mobaxterm远程连接服务器后，找到对应的目录，进行包的安装
```
cd /var/jenkins_home/workspace/helloworld/
npm install
```
![image](https://img-blog.csdnimg.cn/20190106204300286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
5、配置构建后操作
这里需要注意Remote directory的配置，传输后文件的路径是由这里的Remote directory和系统配置里Publish over SSH 配置中的Remote directory构成的，假设你在前面的Publish over SSH中配置了Remote directory为/var/www/test，而在这里配置为/home/ubuntu/workspace/，那么最终的传输路径将会是/var/www/test/home/ubuntu/workspace/。完成后点击保存，接下来就可以构建我们的项目了。
![image](https://img-blog.csdnimg.cn/20190106205359228.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
6、执行构建操作
点击立即构建，等待一会，不出意外的话，项目构建成功，我们可以通过观察 Console Output 来观察项目的执行过程。
![image](https://img-blog.csdnimg.cn/2019010621103278.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
![image](https://img-blog.csdnimg.cn/20190106211059679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
构建成功后的结构多了一个dist目录，至此，web自动化构建的实践便宣告完成了。
![image](https://img-blog.csdnimg.cn/20190106212311816.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
同时，我们通过mobaxterm远程连接到服务器后，可以通过/var/jenkins_home/workspace/helloworld 目录看到jenkins帮我们拉取的源项目文件，通过 /home/ubuntu/workspace/helloworld 目录看到我们发布好的编译文件
![image](https://img-blog.csdnimg.cn/20190107094252526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
![image](https://img-blog.csdnimg.cn/20190107094512211.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
7、构建失败demo分析
上面算的构建过程算是比较顺利的，然后我在实际的操作中，失败的例子也不少，以下是一些失败的构建例子。
![image](https://img-blog.csdnimg.cn/20190106213110543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
a、失败demo1 —— 权限不足
![image](https://img-blog.csdnimg.cn/20190106213212718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
解决办法：
```
chmod -R 777 /var/jenkins_home/workspace
```
b、失败demo2 —— node版本错误
![image](https://img-blog.csdnimg.cn/2019010621341837.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
解决方法：更换合适的版本
![image](https://img-blog.csdnimg.cn/20190106213525464.png)
总的来说，我们可以通过Console Output 中的打印信息来寻找错误原因从而找到解决办法。
## nginx配置
  这一步是可根据个人喜好来做，目的是将通过域名访问到Jenkins的构建页面。上面的过程我们已经可以通过http://94.191.33.76:49002 访问到Jenkins页面，接下来我们修改相关配置，从而通过 http://jenkins.hookyun.cn 访问到我们的Jenkins页面。
1、子域名配置
在域名供应商那里增加一个子域名，我这里在腾讯云增加了一条解析记录
![image](https://img-blog.csdnimg.cn/20190106215541238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)
2、配置nginx 
![image](https://img-blog.csdnimg.cn/2019010621582192.png)
3、重启nginx，完成配置
```
nginx -s reload
```
4、浏览器访问 jenkins.hookyun.cn
![image](https://img-blog.csdnimg.cn/20190107093721855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNzY3MjYz,size_16,color_FFFFFF,t_70)