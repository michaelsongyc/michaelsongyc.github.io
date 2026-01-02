# Spring Boot Starter小结

## 概述
&emsp;&emsp;  Springboot 秉承 "约定大于配置"的理念，通过规范化的文件结构、约定的配置文件等方式，将复杂的pom引用依赖通过stater包等方式隐藏，同时各starter模块之间相互独立，化繁为简，提高工程研发效率。

## spring-boot-starter document
- [spring-boot-document](https://docs.spring.io/spring-boot/docs/current/reference/html/index.html)
- [Starters](https://docs.spring.io/spring-boot/docs/2.7.5/reference/htmlsingle/#using.build-systems.starters)

&emsp;&emsp; Starters are a set of convenient dependency descriptors that you can include in your application. You get a one-stop shop for all the Spring and related technologies that you need without having to hunt through sample code and copy-paste loads of dependency descriptors. For example, if you want to get started using Spring and JPA for database access, include the spring-boot-starter-data-jpa dependency in your project.

&emsp;&emsp; The starters contain a lot of the dependencies that you need to get a project up and running quickly and with a consistent, supported set of managed transitive dependencies.

>What is in a name
> 
> All official starters follow a similar naming pattern; spring-boot-starter-*, where * is a particular type of application. This naming structure is intended to help when you need to find a starter. The Maven integration in many IDEs lets you search dependencies by name. For example, with the appropriate Eclipse or Spring Tools plugin installed, you can press ctrl-space in the POM editor and type “spring-boot-starter” for a complete list.
> 
> As explained in the “Creating Your Own Starter” section, third party starters should not start with spring-boot, as it is reserved for official Spring Boot artifacts. Rather, a third-party starter typically starts with the name of the project. For example, a third-party starter project called thirdpartyproject would typically be named thirdpartyproject-spring-boot-starter. 

## spring-boot-starter source code


## auto-spring-boot-starter demo


## Refer to
[(Springboot document) Creating Your Own Starter](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-auto-configuration.custom-starter)
