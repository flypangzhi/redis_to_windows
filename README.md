## 前言

**其实将redis安装成windows服务的文章目前已经很多了，不过我在根据他们的提示操作的时候还是踩了不少的坑。所以我就参考他们的文档和自身经历写出这篇博客供自己和有需要的童鞋参考。我这里重点要介绍的是在一台windows服务器上部署多个redis出来供多个项目使用。**

 **一、下载安装包**
 

redis:https://github.com/MicrosoftArchive/redis/releases
RedisDesktopManager:
redis可以下载.msi文件或者.zip压缩包，至于两者的区别下方会讲到。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190129171308590.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNTc0NDM1,size_16,color_FFFFFF,t_70)


**二、安装方式**

对于安装方式，其实在上面说的.zip包里的Windows Service Documentation.docx就已经记载有了，总的来说有两种：

**1、使用.msi方式傻瓜式安装，这个直接双击运行，然后在弹出框设置参数即可。**

简单粗暴，具体可以参考博客：[Windows下安装Redis服务](https://www.cnblogs.com/jaign/articles/7920588.html)。不过按照博客操作之后，如果别的电脑使用客户端RedisDesktopManager连接不上你的redis的话，可以参考文章末尾的文章开放端口和修改配置文件，我就是按照这样的操作搞成功的。
优点：简单粗暴，傻瓜式安装。
缺点：使用这种方式并不能在一台电脑上安装多个redis服务，因为每次点击.msi文件的时候它都会去检查是否安装有redis,如果有的话需要先删除才能安装新的，具有很大的局限性。

**2、通过解压.zip包和命令行的安装方式**

使用这种方式会占用一些时间，不过一台电脑可以安装多个redis服务，非常的实用。

**三、使用命令行安装redis步骤**

  **1、将zip包解压到本地文件夹，建议修改文件夹名为redis + -端口号，比如说redis-6679。这样方便以后管理，一目了然。**
  
 **2、修改redis.windows-service.conf的相关配置**

    将port 6379 中的6379修改为你希望的端口，比如6679
    将bind 127.0.0.1 改为#bind 127.0.0.1 。把这个注释是为了客户端可以通过ip连接
    将日志文件 logfile "server_log.txt" 改为server_log_端口号.txt 比如server_log_6679.txt 。方便以后多文件对比
    找到 # requirepass foobared 这行，然后在下方写上你的密码，比如说 requirepass 123456 。那么你的redis登录密码即为123456。

**3、使用cmd命令进入redis目录**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190129175340154.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNTc0NDM1,size_16,color_FFFFFF,t_70)

**4、执行安装命令redis-server --service-install redis.windows-service.conf  --service-name redis-6679**

**备注：文章开头提及的官方文档是采用带有端口命令安装，比如说：redis-server --service-install --service-name redisService1 --port 10001 或者 redis-server --service-install redis.windows-service.conf --service-name redis-6679 --port 10001。其实我并不推荐这种方式，因为就算你指定了配置文件，安装之后也不会对配置文件端口号进行修改，要是以后查看配置文件的话可能会产生误解。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190129180450612.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNTc0NDM1,size_16,color_FFFFFF,t_70)

**5、执行启动命令redis-server --service-start --service-name redis-6679**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190129184130681.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNTc0NDM1,size_16,color_FFFFFF,t_70)

**6、卸载服务命令 redis-server --service-uninstall --service-name redis-6679**

**7、停止服务命令 redis-server --service-stop --service-name redis-6679**

**8、使用客户端输入ip去连接redis并测试连接**

输入登录信息之后点击test connection，如果成功将会弹出下图弹框，否则即是失败。可参考文章末尾的链接开放端口或者修改配置文件并且卸载和重新安装服务。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190129185614683.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyNTc0NDM1,size_16,color_FFFFFF,t_70)

## 结语

**对于安装这个我个人建议还是一个redis一个文件夹的格式，这样方便管理，避免出错。然后如果按照我的方法安装了但是用客户端无法连接，这个时候可以参考下方的连接修改配置文件和卸载重新安装服务即可。通过这种方式我们就可以在一台电脑上部署多个redis了，不管是学习还是内网测试都可以起到很大的帮助。**

## 参考链接

[windows下配置多redis实例](https://blog.csdn.net/tmtongming/article/details/80590079)
[Windows下安装redis服务](https://www.cnblogs.com/xuzhiwei/p/4569315.html)
[开放windows服务器端口](https://blog.csdn.net/zzq900503/article/details/11936379)
[解决不能使用ip访问主机redis](https://blog.csdn.net/sinat_34191046/article/details/82979664)
[redis.windows.conf各项配置参数介绍](https://blog.csdn.net/tian330114067/article/details/78059204)



