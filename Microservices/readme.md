# Information about Micro Services 
[Useful Links](useful_links.md)



------

## Regular Business Service

Default ports

| Port Number | Service                    |
| ----------- | -------------------------- |
| 8761        | Discovery Service (Eureka) |
|             |                            |
|             |                            |

#### Configuration

There are two ways to retrieve the application configuration from  Config Service:

1. specify Config Service URL:  `spring.cloud.config.uri=http://localhost:8888`
2. to use Discovery Service to locate Config Server.

If you prefer to use Discovery Service to locate the Config Server, you can do so by setting `spring.cloud.config.discovery.enabled=true` (the default is `false`). The result of doing so is that client applications all need a `bootstrap.yml` (or an environment variable) with the appropriate discovery configuration.



#### Actuator

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



## Eureka 

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



## Config Service

##### Using GIT Backend

By default, the Config Service uses a Git backend.   It can be configured with:

```properties
spring.cloud.config.server.git.uri=file:///D:/config-repo
```

or

```properties
spring.cloud.config.server.git.uri=https://example.com/my/repo
```

You can control how often the config server will fetch updated configuration data from your Git backend by using `spring.cloud.config.server.git.refreshRate`. The value of this property is specified in seconds. By default the value is 0, meaning the config server will fetch updated configuration from the Git repo every time it is requested.

##### Using File System Backend

There is also a “native” profile in the Config Server that does not use Git but loads the config files from the local classpath or file system (any static URL). Here is a configuration sample for the File System backend:

```properties
spring.profiles.active=native
spring.cloud.config.server.native.searchLocations=file://D:/my_config
```



##### Other Types  of Backends

There are a bunch of other types of possible backends: Vault , JDBC.   See  [this](https://cloud.spring.io/spring-cloud-config/multi/multi__spring_cloud_config_server.html) for more information.



##### Endpoint URLs

The following endpoints are accessible for any configuration 

```
/{application}/{profile}
/{application}-{profile}.yml
/{application}-{profile}.properties
```



On top of that, the *Git*-backed configuration provides the following endpoints:

```
/{application}/{profile}/{git_branch}
/{git_branch}/{application}-{profile}.yml
/{git_branch}/{application}-{profile}.properties
```

where, 

*{application}*  - is the client's application name (required)

*{profile}*  - is the client's current active application profile (required, use 'default' if unknown)



*Note:*  the Config Service provides the latest value from the backend by request, but the client needs to be informed about this change, so it could retrieve it from the  Config Service.  There are a few ways to do that:

1. Manually trigger a refresh event with Spring Boot Actuator POST (***/actuator/refresh***)
2. Using webhooks (spring-cloud-config-monitor). It adds **/monitor** endpoint which could be triggered by Git server providers such as GitHub or Bitbucket.
3. Using Spring Cloud Bus (witch integrates with RabbitMQ or Kafka).