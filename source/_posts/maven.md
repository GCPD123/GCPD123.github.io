---
title: maven
date: 2019-12-07 21:16:03
tags: maven
category: java
---

### 为什么要用maven

![](1.png)

目前开发中存在的问题：

1. 一个项目就是一个工程 
   如果项目非常庞大，就不适合继续使用package来划分模块。最好是每一个模块对应一个项目，利于分工协作。 
   借助于maven就可以将一个项目拆分成多个工程。
2. 项目中需要的jar包必须手动“复制”、”粘贴” 到WEB-INF/lib 项目下 
   带来的问题：同样的jar包文件重复出现在不同的项目工程中，一方面浪费存储空间，另外也让工程比较臃肿。 
   借助Maven，可以将jar包仅仅保存在“仓库”中，有需要使用的工程“引用”这个文件，并不需要重复复制。
3. jar包需要别人替我们准备好，或到官网下载 
   所有知名框架或第三方工具jar包已经按照统一规范放在了Maven的中央仓库中。
4. 一个jar包依赖的其他jar包需要自己手动加到项目中 
   Maven会自动将被依赖的jar包导入进来。

### maven是什么

1. Maven 是 Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理 。Maven 这个单词的本意是：专家，内行。读音是[‘meɪv(ə)n]或[‘mevn]。 
   构建工具的发展：Make→Ant→Maven→Gradle
2. 构建：就是以我们编写的Java代码、框架配置文件、国际化等其他资源文件、jsp页面和图片等静态资源作为“原材料”，去“生产”出一个可以运行的项目的过程。

![](2.png)

在web工程中主要有三个目录：src(存放源文件)、build(存放编译后的class文件)、WebContent(存放页面资源和配置的文件)。工程经过部署后就要在服务器上运行，也就是tomcat上，tomcat对部署的工程有严格的目录要求，对应关系是：**工程名字就是运行的根目录，Chiken是工程名也是tomcat上应用的名字**WebContent目录下的所有文件都会到应用到根目录下，其中build下的内容会到WEB-INF目录下，所谓的类路径说的就是编译后的class文件的路径，即：**tomcat中WEB-INF=build+WEB-INF，应用根目录=WebContent**

总结：应用=WebContent+build 其中build到了WEB-INF中了，因为WEB-INF中的东西不能直接访问，为了安全所以class文件在其中



#### 构建的几个主要环节

①清理：删除以前的编译结果，为重新编译做好准备。 
②编译：将Java源程序编译为字节码文件。 
③测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。 
④报告：将每一次测试后以标准的格式记录和展示测试结果。 
⑤打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java工程对应jar包，Web工程对象war包。 
⑥安装：在Maven环境下特指将打包的结果——Jar包或War包安装到本地仓库中。 
⑦部署：将打包的结果部署到远程仓库或将war包部署到服务器上运行。



### maven工程

![](3.png)

遵守约定的目录结构的意义

我们在开发中如果需要让第三方工具或框架知道我们自己创建的资源在哪，那么基本上就是两种方式： 
①以配置文件的方式明确告诉框架 如 < param-value>classpath:spring-context.xml < /param-value> 
②遵循框架内部已经存在的约定 如log4j的配置文件名规定必须为 log4j.properties 或 log4j.xml ；Maven 使用约定的目录结构

第三方：第一方是JDK、第二方是开发者、第三方是JDK和开发者都无法解决的东西需要其他包来完成

###maven常用命令

【1】mvn clean : 清理 
【2】mvn compile : 编译主程序 
【3】mvn test-compile : 编译测试程序 
【4】mvn test : 执行测试 
【5】mvn package : 打包 
【6】mvn install ： 安装 
【7】mvn site ：生成站点



1. Maven 的核心程序中仅仅定义了抽象的生命周期，但是具体的工作必须有特定的插件来完成。而插件本身不包含在Maven核心程序中。
2. 当我们执行的Maven命令需要用到某些插件时，Maven核心程序会首先到本地仓库中查找。
3. 本地仓库的默认位置：[系统登陆用户的家目录] \ .m2\repository
4. Maven核心程序如果在本地仓库中找不到需要的插件，那么它会自动连接外网，到中央仓库下载。
5. 如果此时无法连接外网，则构建失败。
6. 修改默认本地仓库的位置可以让Maven核心程序到我们事先准备好的目录下查找插件 
   ①找到Maven解压目录\conf\settings.xml 
   ②在setting.xml 文件中找到 localRepository 标签 
   ③将 < localRepository>/path/to/local/repo< /localRepository>从注释中取出 
   ④将标签体内容修改为自定义的Maven仓库目录

### POM

1. 含义：Project Object Model 项目对象模型 
   DOM ：Document Object Model 文档对象模型
2. pom.xml 对于 Maven工程是核心配置文件，与构建过程相关的一切设置都在这个文件中进行配置。 
   重要程度相当于web.xml 对于动态web工程

### 坐标

1. 数学中的坐标： 
   ①在平面中，使用X,Y坐标可以唯一的定位平面中任何一个点。 
   ②在空间中，使用X,Y，Z三个向量可以唯一的定位空间中的任何一个点。

2. Maven的坐标： 
   使用下面三个向量在仓库中唯一定位一个Maven工程

3. ①groupid:公司或组织域名倒序+项目名

   > < groupid>com.atguigu.maven< /groupid>

   ②artifactid:模块名

   > < artifactid>Hello< /artifactid>

   ③version：版本

   > < version>1.0.0< /version>

   坐标又叫“gav”，全部是第一个首字母

   

   Maven 工程的坐标与仓库中路径的对应关系，以spring为例

   > < groupId>org.springframework< /groupId> 
   > < artifactId>spring-core< /artifactId> 
   > < version>4.0.0.RELEASE< /version>
   >
   > org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE.jar

全部一个一个拼起来就可以定位一个文件的位置，所以称作坐标

### 依赖

当 A jar 包用到了 B jar 包中的某些类时，A 就对 B 产生了依赖，这是概念上的描述。Maven解析依赖信息时会到仓库中查找被依赖的jar包。 
对于我们自己开发的Maven工程，要使用mvn install 命令安装后就可以进入仓库。

![](4.png)

#### 依赖的范围

![](5.png)

compile范围的依赖主要在编译阶段有效，即主程序和测试程序都需要编译，所以都有效，有效的意思是：在源码中导入相应的包后可以使用

test范围的依赖只在测试阶段有效，即在主程序的源码中导包后并不能使用，提示没有相应的依赖

compile范围依赖 
》对主程序是否有效：有效 
》对测试程序是否有效：有效 
》是否参与打包：参与 
》是否参与部署：参与 
》典型例子：spring-core

test范围依赖 
》对主程序是否有效：无效 
》对测试程序是否有效：有效 
》是否参与打包：不参与 
》是否参与部署：不参与 
》典型例子：Junit

因为test的包不需要部署到服务器上，所以也不需要打包，打包的目的就是为了部署（编译测试打包安装部署运行）

![](6.png)

provided的依赖意思是：在开发阶段可以使用，打包和部署则由servlet容器提供

》对主程序是否有效：有效 
》对测试程序是否有效：有效 
》是否参与打包：不参与 
》是否参与部署：不参与 
》典型例子：Servlet-api.jar

### 生命周期

1. 各个构建环节执行的顺序：不能打乱顺序，必须按照既定的正确顺序来执行。
2. Maven的核心程序中定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的。
3. Maven核心程序为了更好的实现自动化构建，按照这一特点执行生命周期中各个阶段：不论现在要执行生命周期中的哪一阶段，都是从这个生命周期最初的位置开始执行。
4. Maven有三套相互独立的生命周期，分别是： 
   ①Clean Lifecycle 在进行真正的构建之前进行一些清理工作。 
   ②Default Lifecycle 构建的核心部分，编译、测试、打包、安装、部署等等。 
   ③Site Lifecycle 生成项目报告，站点，发布站点。

他们相互独立。也可以直接运行 mvn clean install site 运行所有这三套生命周期。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行 mvn clean，这个 clean 是 Clean 生命周期的一个阶段。有 Clean 生命周期，也有 clean 阶段。



Default声明周期 
Default 生命周期是 Maven 生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里，只解释一些比较重要和常用的阶段： 
validate 
generate-sources 
process-sources 
generate-resources 
process-resources 复制并处理资源文件，至目标目录，准备打包。 
compile 编译项目的源代码。 
process-classes 
generate-test-sources 
process-test-sources 
generate-test-resources 
process-test-resources 复制并处理资源文件，至目标测试目录。 
test-compile 编译测试源代码。 
process-test-classes 
test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。 
prepare-package 
package 接受编译好的代码，打包成可发布的格式，如 JAR。 
pre-integration-test 
integration-test 
post-integration-test 
verify 
install 将包安装至本地仓库，以让其它项目依赖。 
deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

**总结：编译 测试 打包 安装 部署运行**

插件和目标 
Maven的核心仅仅定义了抽象的生命周期，具体的任务都是交由插件完成的。 
每个插件都实现多个功能，每个功能就是一个插件目标 
Maven的生命周期与插件目标相互绑定，以完成某个具体的构建任务。 
可以将目标看做“调用插件功能的命令”

例如：compile 就是插件 maven-compiler-plugin 的一个目标；pre-clean 是插件 maven-clean-plugin 的一个目标。

插件：目标（一个插件可以有多个目标）