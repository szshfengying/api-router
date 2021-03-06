# api-router
api路由分发组件，适用于有接口编号等类似概念的服务。封装公共的分发逻辑，使相关人员更专注于业务开发

## 背景

对于业务系统而言，需要向外提供多个服务，此时，一般可以有以下两种选择

1）每个服务单独一个`URL`，这种情况的优点是每个服务的参数类型是可控的(能知道具体参数的类型，并能借助`JSR-303`检验框架进行数据校验)；缺点就是服务方和调用方需要维护对个`URL`地址。

2）向外只提供一个`URL`，内部具体执行的服务，通过入参来标识，如通过`apiNo`标识需要调用的具体服务或者是将`apiNo`作为`URL`路劲的一部分，即`baseUrl/service/{apiNo}`；一般情况每个服务需要的参数都不一样，因此无法定义一个统一的类来标识，这种情况下，可以使用`map`来接收入参。并且只需要维护一个`url`地址,但内部的参数检验需要作额外的序列化工作。

## 综合方案

结合上面的两种方案，提出一种居中的方案：对外只提供一个服务`URL`，统一入参格式为：

```
{
  "head":{
    "apiNo":"api001",
    "version":"V1.0.1",
    ......
  },
  "bady":{
  ......
  }
}

```

其中`head`提供了与业务无关的参数，如接口编号、版本号、请求时间等，这部分数据是固定的，`body`是具体的业务参数，可以通过Map来接收，此时，整个系统的入参类型可以定义为：

```
public class ApiParamter{
   private Head head;
   private Map body;
   ......
}
```

此时整个调用流程是：

![image](https://github.com/wdhxwz/api-router/blob/master/images/api%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.jpg)

现在问题来了：怎么知道有哪些api并且具体api对应的参数类型？

ApiRouter可以解决上面的问题，并且对整个接口的调用流程进行抽取，封装，当业务系统需要这个路由功能的时候，直接对接，就可以使用了，避免重复的工作。


## 实现方案

- 通过注解的方式定义每个接口的公共参数：apiNo/version/module...,该注解同时会标志为spring的@Service
- 服务执行者采用模板模型设计,并且使用泛型,这样每个接口在实现的时候，可以指定入参类型和出参类型
- 基于spring-framework，程序启动时，从spring-context中获取指定注解的bean，将其缓存起来
- 提供异常类和响应码接口，可以根据响应码构造异常，业务方定义自己响应码需继承响应码接口

![image](https://github.com/wdhxwz/api-router/blob/master/images/%E6%95%B4%E4%BD%93%E7%BB%93%E6%9E%84.jpg)

## 业务系统整合

提供两种整合方式，dubbo方式、http方式

### HTTP整合方式

- 添加依赖

- 配置ApiRouter

- 编写面向外部接口

- 编写业务接口


## 具体设计

### 基于模板方法模式设计

接口在组织上有两种方式：1)一个文件表示一个模板,不同的方法表示不同的接口逻辑 2)一个文件表示一个接口逻辑，模块通过包来组织

在该组件的设计上，采用第二种方式。

在第二种方式的基础上，采用模板方法模式进行设计，相关类图如下：




