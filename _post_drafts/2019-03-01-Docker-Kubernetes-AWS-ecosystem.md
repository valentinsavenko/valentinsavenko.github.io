---
layout: post
last-modified: '2019-01-09T01:00:00-04:00'

title: "Puppetforge module ecosystem"
subtitle: "Continuously deploy your module to the Puppet Forge, for free, with Travis CI"
image:
    feature: puppet-module-ecosystem/forge-wide.jpg
    credit: jenszhonk
    creditlink: https://pixabay.com/en/users/jenszhonk-1418792/
---
The Puppet community is all about automation and self-explanatory, modern approaches to infrastucture and configuration but if you decide to actually go thought all steps that are needed to have a well documented, tested and published puppet module, you'll need to read quite a while, from different sources. I try to abbreviate that process and collect _most_ relevant steps here on one page.

## 1. Create a module with the Puppet Development Kit

> Based on official docu: [installation](https://puppet.com/docs/pdk/1.x/install_pdk_osx.html) ,[usage](https://puppet.com/docs/pdk/1.x/pdk_creating_modules.html)
> [Spring docker tutorial](https://spring.io/guides/gs/spring-boot-docker/)


I assume you are using a Mac. If not, changes may apply and you should check the source material above.
<!--more-->
First, let’s install the Puppet Dev Kit and create the module:

```bash
  git clone https://github.com/spring-guides/gs-spring-boot-docker.git
  cd gs-spring-boot-docker/complete

  # install
  ./gradlew build docker                                                                                                               master ◼
  docker images                                                                                                           ✘ 1 master ◼

  REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
  springio/gs-spring-boot-docker   latest              c7d1396d0304        8 minutes ago       121MB

  docker run -p 8080:8080 -t springio/gs-spring-boot-docker                                                                                                               master ◼

    .   ____          _            __ _ _
  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
  ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
    '  |____| .__|_| |_|_| |_\__, | / / / /
  =========|_|==============|___/=/_/_/_/
  :: Spring Boot ::        (v2.0.5.RELEASE)

  2019-02-26 16:23:09.027  INFO 1 --- [           main] hello.Application                        : Starting Application on bf51da2bfa78 with PID 1 (/app started by root in /)
  2019-02-26 16:23:09.030  INFO 1 --- [           main] hello.Application                        : No active profile set, falling back to default profiles: default
  2019-02-26 16:23:09.127  INFO 1 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@1c93084c: startup date [Tue Feb 26 16:23:09 GMT 2019]; root of context hierarchy
  2019-02-26 16:23:10.494  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
  2019-02-26 16:23:10.572  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
  2019-02-26 16:23:10.572  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.34
  2019-02-26 16:23:10.609  INFO 1 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64/server:/usr/lib/jvm/java-1.8-openjdk/jre/lib/amd64:/usr/lib/jvm/java-1.8-openjdk/jre/../lib/amd64:/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib]
  2019-02-26 16:23:10.699  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
  2019-02-26 16:23:10.700  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1577 ms
  2019-02-26 16:23:10.756  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
  2019-02-26 16:23:10.761  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
  2019-02-26 16:23:10.763  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
  2019-02-26 16:23:10.763  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
  2019-02-26 16:23:10.763  INFO 1 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
  2019-02-26 16:23:10.971  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
  2019-02-26 16:23:11.312  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@1c93084c: startup date [Tue Feb 26 16:23:09 GMT 2019]; root of context hierarchy
  2019-02-26 16:23:11.419  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/]}" onto public java.lang.String hello.Application.home()
  2019-02-26 16:23:11.429  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
  2019-02-26 16:23:11.430  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
  2019-02-26 16:23:11.495  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
  2019-02-26 16:23:11.496  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
  2019-02-26 16:23:11.739  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
  2019-02-26 16:23:11.814  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
  2019-02-26 16:23:11.820  INFO 1 --- [           main] hello.Application                        : Started Application in 3.131 seconds (JVM running for 3.51)
  2019-02-26 16:24:28.405  INFO 1 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
  2019-02-26 16:24:28.405  INFO 1 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization started
  2019-02-26 16:24:28.434  INFO 1 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization completed in 29 ms
  ^C


  docker ps -a                                                                                                         ✘ 130 master ◼
  CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS                           PORTS                                        NAMES
  bf51da2bfa78        springio/gs-spring-boot-docker   "java -cp app:app/li…"   4 minutes ago       Up 4 minutes                     0.0.0.0:8080->8080/tcp                       awesome_northcutt


  docker rm 39


  minikube start
  kubectl run hello-minispring --image=springio/gs-spring-boot-docker --port=8080
  minikube dashboard
  kubectl expose deployment hello-minispring --type=NodePort
  kubectl service hello-minispring
```



minikube stuff:


```




```


EKS stuff:
> https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html

