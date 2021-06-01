### 1 Maven 简介

maven是一个项目管理工具。

- 依赖管理系统
- 项目生命周期
- 项目模型
- 包管理

### 2 项目构建

  项目构建流程：

【清理项目】→【编译项目】→【测试项目】→【生成测试报告】→【打包项目】→【部署项目】

### 3 Maven项目的目录结构

![image-20210429173442309](https://tva1.sinaimg.cn/large/008i3skNly1gq0qz99mjkj30fq0kimy7.jpg)

- `src/main/webapp` 如果是web项目，则该目录是web应用源代码所在的目录，比如html文件和web.xml等都在该目录下。

### 4 maven仓库

分为本地仓库（~/.m2/repository）、中央仓库（http://repo1.maven.org/maven2）、第三方仓库。pom.xml查找jar包顺序 先本地，接着中央仓库，最后查找第三方。

第三方仓库地址在settings.xml里面进行配置。

注：以`-SNAPSHOT`结尾的版本号会被Maven视为开发版本，开发版本每次都会重复下载，这种SNAPSHOT版本只能用于内部私有的Maven repo，公开发布的版本不允许出现SNAPSHOT。

### 5 打包

打包类型有jar，war，ear。

- jar：jar文件包括java普通类、资源文件和普通文件，在maven中即是打包src/main/java和src/main/resources资源文件夹下的所有文件。
- war：war文件包含全部的web应用程序，包含 Servlet、HTML页面、Java类、图像文件，以及组成Web应用程序的其他资源。

### 6 依赖范围

| scope    | 说明                                          | 示例            |
| :------- | :-------------------------------------------- | :-------------- |
| compile  | 编译时需要用到该jar包（默认）                 | commons-logging |
| test     | 编译Test时需要用到该jar包                     | junit           |
| runtime  | 编译时不需要，但运行时需要用到                | mysql           |
| provided | 编译时需要用到，但运行时由JDK或某个服务器提供 | servlet-api     |

### 7 生命周期及插件

生命周期(lifecycle)是由一系列的阶段(phase)组成。以内置的生命周期`default`为例，它包含以下phase：

- validate
- initialize
- generate-sources
- process-sources
- generate-resources
- process-resources
- compile
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-resources
- process-test-resources
- test-compile
- process-test-classes
- test
- prepare-package
- package
- pre-integration-test
- integration-test
- post-integration-test
- verify
- install
- deploy

运行`mvn package`，Maven就会执行`default`生命周期，它会从开始一直运行到`package`这个phase为止：

- validate
- ...
- package

运行`mvn compile`，Maven也会执行`default`生命周期，但这次它只会运行到`compile`，即以下几个phase：

- validate
- ...
- compile

> 即使用`mvn`这个命令时，后面的参数是phase，Maven自动根据生命周期运行到指定的phase结束。

生命周期是`clean`，它会执行3个phase：

- pre-clean
- clean （注意这个clean不是lifecycle而是phase）
- post-clean

**Goal**

一个phase是由若干个goal执行的。

| 执行的Phase | 对应执行的Goal                     |
| :---------- | :--------------------------------- |
| compile     | compiler:compile                   |
| test        | compiler:testCompile surefire:test |

其实我们类比一下就明白了：

- lifecycle相当于Java的package，它包含一个或多个phase；
- phase相当于Java的class，它包含一个或多个goal；
- goal相当于class的method，它其实才是真正干活的。

在实际开发过程中，**经常使用的命令**有：

`mvn clean`：清理所有生成的class和jar；

`mvn clean compile`：先清理，再执行到`compile`；

`mvn clean test`：先清理，再执行到`test`，因为执行`test`前必须执行`compile`，所以这里不必指定`compile`；

`mvn clean package`：先清理，再执行到`package`。

### 8 Maven Wrapper

是用来给项目指定一个特定maven版本的。

### 9 JAR包冲突问题排查及解决方案

包冲突：两个包依赖的版本不同，项目具体使用哪个版本，将会取决于类加载器。

排查方法：

#### 1 mvn dependency:tree

![image-20210429200425488](https://tva1.sinaimg.cn/large/008i3skNly1gq0vazzpysj31i20e478t.jpg)

- 最后写着`compile`的就是编译成功的
- 最后写着`omitted for duplicate`的就是有`JAR`包被重复依赖了，但是`JAR`包的版本是一样的
- 最后写着`omitted for conflict with xx`的，说明和别的`JAR`包版本冲突了，该行的`JAR`包不会被引入

**解决冲突**：使用`exclusion`标签将冲突的`JAR`排除。(解决冲突的策略是:就近原则，即离根近的依赖被采纳)

![image-20210429200607259](https://tva1.sinaimg.cn/large/008i3skNly1gq0vcrmuogj31h60kutcq.jpg)

#### 2 IDEA中的Show Dependency功能



### 10 其他

SNAPSHOT表示处于不稳定的开发版本，每次构建时maven都回去尝试更新它，而没有SNAPSHOT标记的，则不会去更新它，除非改变版本号。

