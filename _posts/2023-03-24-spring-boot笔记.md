---
layout: post
title: spring boot笔记
date: 2023-03-24 22:23 +0800
excerpt: 'spring boot, spring cloud的笔记'
---
# 基本概念
1. `bean`
2. `Guice`依赖注入框架。
   1. spring 中，先进行注解注入，然后才是 xml 注入，因此如果注入的目标相同，后者会覆盖前者。
   2. Spring 默认是不启用注解的。如果想使用注解，需要先在 xml 中启动注解。 启动方式：在 xml 中加入一个标签，`<context:annotation-config/>`
3. 在@Autowired注解中，提到了如果发现有多个候选的 bean 都符合修饰类型，Spring 就会抓瞎了。可以通过@Qualifier指定 bean 名称来锁定真正需要的那个 bean。
4. spring-boot-actuator (opens new window)- 展示 Spring Boot 应用监控。
5. spring-boot-aop (opens new window)- 展示 Spring Boot 中应用 AOP 编程。
6. spring-boot-async (opens new window)- 展示在 Spring Boot 中使用异步接口。
7. spring-boot-banner (opens new window)- 展示在 Spring Boot 中定制启动时的输出 Logo。
8. spring-boot-bean (opens new window)- 展示 Spring Boot 中应用 Lombok，简化大量的 getter、setter 、toString 等模板化代码；以及应用 Dozer 来简化大量的 JavaBean 互相转换代码。
9. spring-boot-profile (opens new window)- 展示 Spring Boot 中根据 profile 使程序在不同的环境下执行不同的行为。
10. spring-boot-property (opens new window)- 全方位的演示 Spring Boot 加载属性的方式：记载 properties 和 yaml 两种文件；通过 @Value、@ConfigurationProperties、Environment 读取属性。

# scripts
1. `mvn clean package -DskipTests=true`
2. `mvn dependency:tree`

# 环境搭建
1. `jenv versions`, 列出所有本地已经安装的版本
2. `jenv add /Library/Java/JavaVirtualMachines/openjdk-8.jdk/Contents/Home`, 添加新版本到jenv里面去
3. `jenv local openjdk64-19.0.1`, 切换版本
4. 
# Ref
1. [Spring Framework简介](https://dunwu.github.io/spring-tutorial/pages/9d3091/#spring-framework-%E7%AE%80%E4%BB%8B)
2. [spring boot各种库的解释](https://dunwu.github.io/spring-boot-tutorial/)
3. [Spring Boot核心概念](https://dunwu.github.io/spring-tutorial/pages/68097d/#spring-bean-%E5%AE%9A%E4%B9%89)
4. [Java Tutorial, 讲java, maven等基本知识](https://dunwu.github.io/java-tutorial/)
5. [Lombok入门](https://github.com/dunwu/spring-boot-tutorial/tree/master/codes/core/spring-boot-bean)