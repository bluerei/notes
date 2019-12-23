# Information about Micro Services 
[Useful Links](useful_links.md)



------

### Actuator

Spring Boot includes a number of additional features to help you monitor and manage your application when you push it to production. You can choose to manage and monitor your application by using HTTP endpoints or with JMX. Auditing, health, and metrics gathering can also be automatically applied to your application.



To add the actuator to a Maven based project, add the following ‘Starter’ dependency:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

See [this](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html) for more.



By default just a few endpoints exposed through the HTTP.  In order to expose everything in HTTP, use the following setting:

```properties
management.endpoints.web.exposure.include=*
```

Note: The Endpoints may contain sensitive information, careful consideration should be given about when to expose them. 

##### Logfile

By default the logfile is not accessible via actuator endpoints and therefore not visible in Spring Boot Admin. In order to enable the logfile actuator endpoint you need to configure Spring Boot to write a logfile, either by setting `logging.path` or `logging.file`.

Here is an example which enables storing log to the file. 

```properties
logging.file=/var/log/sample-boot-application.log 
logging.pattern.file=%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){faint} %clr(%5p) %clr(${PID}){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n%wEx 
```



### Eureka

Default port:  http://localhost:8761/

By default every Eureka client is trying to register itself with real hostname and  defined DNS suffix (if exist). Sometimes it does not work properly. So in order to  start using IP address instead of  hostname, use the following setting in all Eureka clients configurations (including Eureka itself )

```properties
eureka.instance.prefer-ip-address=true
```

 Or as alternative, the hostname could be specified with 

```properties
eureka.instance.hostname=localhost
```

Also, specifying an IP address can be done by

```properties
eureka.instance.ip-address=IP_OF_MACHINE_ON_WHICH_RUNNING
```

