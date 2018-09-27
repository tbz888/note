> Maven 是一个项目管理和构建自动化工具

- Snapshot开发版
- Release稳定版 

| **目录**                                                | **目的**                        |
| ------------------------------------------------------- | ------------------------------- |
| ${basedir}                                              | 存放 pom.xml和所有的子目录      |
| ${basedir}/src/main/java                                | 项目的 java源代码               |
| ${basedir}/src/main/resources                           | 项目的资源，比如说 property文件 |
| ${basedir}/src/test/java                                | 项目的测试类，比如说 JUnit代码  |
| ${basedir}/src/test/resources  | 测试使用的资源                  |
| ${basedir}/target                                       | JAR 文件                        |
| ${basedir}/target/classes                                                        | 编译后 的 classes               |

1. groupId, artifactId, version 叫作 maven 坐标，它能唯一的确定一个项目。
2. 大项目一般会分成几个子项目。在这种情况下，每个子项目就会有自己的 POM 文件，然后它们会有一个共同的父项目。这样只要构建父项目就能够构建所有的子项目了。子项目的 POM 会继承父项目的 POM。

## Maven Plugins

​     mvn 本身不会做太多的事情，它不知道怎么样编译或者怎么样打包。它把构建的任务交给插件去做。插件定义了常用的构建逻辑，能够被重复利用。

## Maven Lifecycle

​     插件目标可以绑定到生命周期阶段上。一个生命周期阶段可以绑定多个插件目标。当 maven 在构建过程中逐步的通过每个阶段时，会执行该阶段所有的插件目标。
