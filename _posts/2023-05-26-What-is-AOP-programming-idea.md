---
layout: post
title: "转：什么是AOP编程思想"
author: "Kai"
header-style: text
tags:
  - 编程思想
---
转自：https://www.duidaima.com/Group/Topic/ArchitecturedDesign/12108

AOP 的核心思想是将横切关注点抽象为一个独立的模块（称之为“切面”），然后在需要应用它的地方进行调用。比如，在需要记录日志的方法中，我们可以定义一个切面来负责日志记录，这样所有调用该方法的地方都会被自动添加上日志功能，而不必修改原有方法。AOP 通过使用诸如“切点”、“连接点”、“通知”等概念，使得开发人员可以灵活地控制切面的应用范围和时机。

AOP 的常用实现方式是利用代理对象来实现切面功能。在 Java 领域中，常见的 AOP 框架有 Spring AOP 和 AspectJ 等。除了 Java，AOP 的思想还可以应用于其它编程语言和平台。

## Spring AOP 和 AspectJ AOP
Spring AOP 和 AspectJ AOP 是两种不同的 AOP 实现。Spring AOP 基于动态代理实现，是 Spring 框架中的 AOP 实现，主要用于解决 Spring 容器中 Bean 的横切关注点问题。由于使用了动态代理，所以只支持方法级别的切面（即只能织入方法的执行）。尽管 Spring AOP 的性能略逊于 AspectJ，但对于大部分应用来说，性能影响不大。

相比之下，AspectJ AOP 是一个独立的、功能更强大的 AOP 实现。它不仅支持方法级别的切面，还支持字段、构造器等其他切面，并可通过编译时织入或加载时织入的方式实现 AOP。这使得 AspectJ 比 Spring AOP 更加灵活和强大。同时，Spring 可以与 AspectJ 结合使用，以提供更强大的 AOP 功能。

## 实现 AOP 的方式
- 动态代理：通过代理模式，为目标对象生成一个代理对象，然后在代理对象中实现横切关注点的织入。动态代理可以分为JDK动态代理（基于接口）和 CGLIB 动态代理（基于类）。
- 编译时织入：在编译阶段，通过修改字节码实现 AOP。AspectJ 的编译时织入就是这种方式。
- 类加载时织入：在类加载阶段，通过修改字节码实现 AOP。AspectJ 的加载时织入就是这种方式。

AOP的实现方式取决于具体需求和技术选型。对于 Spring 应用来说，通常可以使用 Spring AOP 满足大部分需求，如果需要更强大的 AOP 功能，可以考虑使用 AspectJ。

## AOP 的相关概念
![20240121220227](https://raw.githubusercontent.com/fannkaii/MyPicBed/master/images/20240121220227.png)

## 实战1
使用spring的AOP，实现日志切面，打印每次方法的入参和返回。
首先，我们需要在Spring配置文件中启用AOP：
```xml
<beans>
    <aop:aspectj-autoproxy />
    <!-- 其他配置 -->
</beans>
```

然后，我们可以创建一个切面类来实现日志记录：
```java
@Aspect
@Component
public class LoggingAspect {
    private static final Logger LOGGER = LoggerFactory.getLogger(LoggingAspect.class);
    @Pointcut("execution(* com.example.*.*(..))")
    public void logMethod() {}
    @Before("logMethod()")
    public void logMethodEntry(JoinPoint joinPoint) {
        LOGGER.info("Entering method: {} with arguments: {}", joinPoint.getSignature().getName(), Arrays.toString(joinPoint.getArgs()));
    }
    @AfterReturning(pointcut = "logMethod()", returning = "result")
    public void logMethodExit(JoinPoint joinPoint, Object result) {
        LOGGER.info("Exiting method: {} with result: {}", joinPoint.getSignature().getName(), result);
    }
}
```

在上面的代码中，我们定义了一个切点 logMethod()，它匹配所有 com.example 包下的方法。然后，我们使用 @Before 和 @AfterReturning 注解来分别记录方法的入参和返回值。在 JoinPoint 对象中，我们可以获取方法的签名和参数。

最后，我们可以在需要记录日志的方法上添加 @LogMethod 注解来启用日志切面：

```java
@Service
public class MyService {
    @LogMethod
    public void doSomething(String arg1, int arg2) {
        // 方法实现
    }
}
```

这样，每次调用 doSomething 方法时，日志切面都会记录方法的入参和返回值。对于 Spring Boot 项目，实现日志切面的方法与普通的 Spring 项目类似。只需要在 Spring Boot 的配置类上添加 @EnableAspectJAutoProxy 注解即可启用 AOP。

下面是一个示例：
```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
    // 其他配置
}
```

然后，我们可以创建一个切面类来实现日志记录：
```java
@Aspect
@Component
public class LoggingAspect {
    // 堆代码 duidaima.com
    private static final Logger LOGGER = LoggerFactory.getLogger(LoggingAspect.class);
    @Pointcut("execution(* com.example.*.*(..))")
    public void logMethod() {}
    @Before("logMethod()")
    public void logMethodEntry(JoinPoint joinPoint) {
        LOGGER.info("Entering method: {} with arguments: {}", joinPoint.getSignature().getName(), Arrays.toString(joinPoint.getArgs()));
    }
    @AfterReturning(pointcut = "logMethod()", returning = "result")
    public void logMethodExit(JoinPoint joinPoint, Object result) {
        LOGGER.info("Exiting method: {} with result: {}", joinPoint.getSignature().getName(), result);
    }
}
```

在上面的代码中，我们定义了一个切点 logMethod()，它匹配所有 com.example 包下的方法。然后，我们使用 @Before 和 @AfterReturning 注解来分别记录方法的入参和返回值。在 JoinPoint 对象中，我们可以获取方法的签名和参数。

最后，我们可以在需要记录日志的方法上添加 @LogMethod 注解用日志：
```java
@Service
public class MyService {
    @LogMethod
    public void doSomething(String arg1, int arg2) {
        // 方法实现
    }
}
```
这样，每次调用 doSomething 方法时，日志切面都会记录方法的入参和返回值。

## 实战2
使用AspectJ AOP，实现日志切面，打印每次方法的入参和返回。使用 AspectJ AOP 结合 Spring Boot 也是非常简单的。下面是一个示例：
首先，我们需要在 pom.xml 文件中添加 AspectJ 相关的依赖：
```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.6</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
```

然后，我们可以创建一个切面类来实现日志记录：
```java
@Aspect
@Component
public class LoggingAspect {
    private static final Logger LOGGER = LoggerFactory.getLogger(LoggingAspect.class);
    @Pointcut("execution(* com.example.*.*(..))")
    public void logMethod() {}
    @Before("logMethod()")
    public void logMethodEntry(JoinPoint joinPoint) {
        LOGGER.info("Entering method: {} with arguments: {}", joinPoint.getSignature().getName(), Arrays.toString(joinPoint.getArgs()));
    }
    @(pointcut = "logMethod()", returning = "result")
    public void logMethodExit(JoinPoint joinPoint, Object result) {
        LOGGER.info("Exiting method: {} with result: {}", joinPoint.getSignature().getName(), result);
    }
}
```
在上面的代码中，我们定义了一个切点 logMethod()，它匹配所有 com.example 包下的方法。然后，我们使用 @Before 和 @AfterReturning 注解来分别记录方法的入参和返回值。在 JoinPoint 对象中，我们可以获取方法的签名和参数。

最后，我们需要在 Spring Boot 的配置类上添加 @EnableAspectJAutoProxy 注解来启用 AspectJ AOP：
```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
    // 其他配置
}
```
这样，我们就可以在需要记录日志的方法上添加 @LogMethod 注解用日志：
```java
@Service
public class MyService {
    @LogMethod
    public void doSomething(String arg1, int arg2) {
        // 方法实现
    }
}
```
这样，每次调用 doSomething 方法时，日志切面都会记录方法的入参和返回值。

## 总结
好的，下面是 AOP 的总结：
AOP（Aspect-Oriented Programming）是一种编程范式，它通过将横切关注点（如日志记录、事务管理等）从业务逻辑中分离出来，以模块化的方式实现这些关注点。AOP 的核心思想是将程序的功能分解成不同的关注点，然后通过切面将这些关注点模块化，从而提高代码的可维护性和可重用性。

## AOP 的主要概念包括：
- 切面（Aspect）：横切关注点的模块化，它包括切点和通知。
- 切点（Pointcut）：程序中需要被拦截的方法或者类。
- 通知（Advice）：在切点处执行的代码，包括前置通知、后置通知、异常通知、最终通知和环绕通知。
- 连接点（Join Point）：程序中可以被拦截的点，通常是方法调用或者异常处理等。
- 织入（Weaving）：将切面应用到目标对象并创建新的代理对象的过程。

## AOP 的实现方式包括：
- 静态代理：手动编写代理类，将切面代码硬编码到代理类中。
- 动态代理：使用 JDK 动态代理或者 CGLIB 动态代理生成代理对象，将切面代码动态织入到代理对象中。
- AspectJ：一种基于 Java 语言的 AOP 框架，它提供了更加灵活和强大的 AOP 功能，支持编译时织入和运行时织入两种方式。

## AOP 的优点包括：
- 提高代码的可维护性和可重用性，将横切关注点从业务逻辑中分离出来，使得代码更加模块化。
- 降低代码的耦合度，将不同的关注点分离开来，使得代码更加灵活和可扩展。
- 提高代码的可读性和可理解性，将关注点的代码集中在一起，使得代码更加清晰和易于理解。

## AOP 的缺点包括：
- 增加了代码的复杂度，需要额外的学习和理解成本。
- 可能会影响程序的性能，特别是在运行时织入切面时，会增加额外的开销。
- 可能会导致调试和排错变得更加困难，特别是在切面代码中存在错误时，可能会影响整个程序的运行
