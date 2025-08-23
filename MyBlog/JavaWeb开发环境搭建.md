文章具有时效性，如果哪个环节不适用了，可在评论区留言，红豆泥阿里嘎多。  
下文所有的安装包和工具都都可以通过迅雷下载。
[下载链接](https://pan.xunlei.com/s/VOVVXfVylUR2UyvcuUkpCIFsA1?pwd=dxjw#)
# Java
在[华为镜像站](https://repo.huaweicloud.com:8443/artifactory/java-local/jdk/)选择合适的jdk版本并安装。  
使用JavaEnvironmentVariablesManager程序可以快速设置java环境变量，需要以管理员身份运行。
![image.png](/image/4d868c7d-581a-4386-b8aa-9e6af8b5f337.png)
# Intellij IDEA
解压IDEA2025，总共有两个文件。
![image.png](/image/6fe3ee2b-8de4-4033-b877-c7dc3584aefe.png)
运行安装程序，先安装完成IDEA。  
然后将解压后文件夹里的jetbraActivation文件夹复制到 C盘根目录
![image.png](/image/1899852f-e78f-432d-96b5-25390de6f73b.png)
进入`./jebraActivation/script`文件夹，双击运行`install-current-user.vbs`。
![image.png](/image/6fcb5ae4-4c01-4134-a4f8-e31040a90c3d.png)  
点击确定并等待`Done`窗口弹出。  
![image.png](/image/cb96fa27-e9fa-472c-b569-e1d8f9605160.png)  
运行IDEA，设置语言、用户协议、隐私协议等操作后进入`Paid license`界面。
![image.png](/image/de3b5969-ce21-423b-a3f4-cb36d0e7afa3.png)
进入`./jebraActivation/Activation_Code/`文件夹，打开 `IntelliJ IDEA.txt`，复制激活码。
![image.png](/image/31a12179-8da8-4a1a-90f7-52e32a446a00.png)
提交后，许可证到期时间变为2099/12/31，激活成功。
![image.png](/image/73caf137-805b-460e-aca4-032a83512115.png)
# Git
使用IDEA Clone Repository，提示Git is not installed，点击Download and install完成安装。
![image.png](/image/6a9637e1-332a-4926-bd57-275e6058d489.png)
![image.png](/image/71c7896f-82fd-402f-a921-f38e19c1900a.png)
![image.png](/image/ee2e3727-cbbc-4e01-b5ca-ea2708d1f2aa.png)
# Maven
IDEA默认安装了Maven，我们只需修改配置。
新增或修改配置文件`C:/Users/<你的用户名>/.m2/settings.xml`，主要修改本地仓库位置和依赖镜像。
```xml
<settings>
    <!-- 本地仓库路径配置 -->
    <localRepository>D:/maven-repo</localRepository>

    <mirrors>
        <!-- 配置阿里云 Maven 镜像 -->
        <mirror>
            <id>aliyun</id>
            <mirrorOf>*</mirrorOf>
            <name>Aliyun Maven Repository</name>
            <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
    </mirrors>

    <profiles>
        <profile>
            <id>defaultProfile</id>
            <repositories>
                <!-- 阿里云中央仓库 -->
                <repository>
                    <id>aliyun-central</id>
                    <url>https://maven.aliyun.com/repository/public</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </repository>

                <!-- 阿里云JCenter仓库 -->
                <repository>
                    <id>aliyun-jcenter</id>
                    <url>https://maven.aliyun.com/repository/jcenter</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </repository>

                <!-- 阿里云Google仓库 -->
                <repository>
                    <id>aliyun-google</id>
                    <url>https://maven.aliyun.com/repository/google</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </repository>

                <!-- 阿里云Spring仓库 -->
                <repository>
                    <id>aliyun-spring</id>
                    <url>https://maven.aliyun.com/repository/spring</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                </repository>
            </repositories>
        </profile>
    </profiles>

    <activeProfiles>
        <activeProfile>defaultProfile</activeProfile>
    </activeProfiles>
</settings>
```
# Naivcat
去[官网](https://www.navicat.com.cn/download/navicat-premium)下载Navicat Premium 17，正常试用安装。
试用14天后可以运行bat脚本，重置14天试用期。
# XShell & XFtp
[https://www.xshell.com/zh/free-for-home-school/](https://www.xshell.com/zh/free-for-home-school/)提供了家庭/学校的免费许可证版本。  
在安装界面可以看到Personal标识。  
![image.png](/image/b40d87f6-ebf2-4204-a513-8084a16ba9f2.png)  
安装完成运行需要输入姓名和邮箱进行个人账号的注册。  
![image.png](/image/89d641e1-6317-40c6-8688-1b2f068527e7.png)
![image.png](/image/785c8ece-453e-45db-a725-3c65840f4955.png)
![image.png](/image/733fb0b7-cb1e-4110-bddb-bb82d712ec5c.png)
注册成功后再次启动XSHELL。
![image.png](/image/95d15609-e60b-4260-91ee-6e8cd2b9dc5e.png)
# Node.js
去[官网](https://nodejs.cn/download/)下载合适的版本后安装，安装完成后设置数据源，并下载pnpm。
![image.png](/image/19a03c85-fb59-47fa-9b94-fd2c0c71b088.png)
```shell
#查询当前使用的镜像源
npm get registry
#设置为淘宝镜像源 
npm config set registry https://registry.npmmirror.com/
#验证设置
npm get registry
#还原为官方源
npm config set registry https://registry.npmjs.org/ 
#安装pnpm
npm install -g pnpm
#设置pnpm目录
pnpm config set store-dir D:\pnpm-store
pnpm config set cache-dir D:\pnpm-store\cache
pnpm config set global-dir D:\pnpm-store\global
pnpm config set state-dir D:\pnpm-store\state
```
