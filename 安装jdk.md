# 安装 jdk 

## 1、下载 jdk

官网下载地址：<https://www.oracle.com/technetwork/java/javase/downloads/index.html>

jdk1.8： <https://www.oracle.com/java/technologies/javase-java-archive-javase8-downloads.html>

jdk1.6：<https://www.oracle.com/java/technologies/javase-java-archive-javase6-downloads.html>

下载旧版本可能需要账号密码，以下资源来自网络：

账号：liwei@xiaostudy.com

密码：OracleTest1234

来自：<https://www.cnblogs.com/xiaostudy/p/9940167.html>

账号：541509124@qq.com

密码：LR4ever.1314

来自：<https://blog.csdn.net/u012789986/article/details/72515324>

## 2、安装jdk

双击exe，一直下一步直到安装完成即可，安装过程中会弹出另一个安装窗口（jre的安装），也是下一步到安装完成即可

## 3、配置java环境

右键 **我的电脑** ——>  **属性** ——> **高级系统设置** ——> **高级** ——> **环境变量** 

在系统变量中添加以下两条环境变量：

JAVA_HOME : jdk目录，安装时未修改的话，在：C:\Program Files\Java

CLASSPATH : .;%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar (完全复制即可)

然后**编辑**Path，在里面添加两条，

%JAVA_HOME%\bin;

%JAVA_HOME%\jre\bin

## 4、验证安装是否成功

在 cmd 中，执行 java 和 javac ，若执行成功则安装完成

提示：打开 cmd ：win+r ——> 输入cmd ——>回车