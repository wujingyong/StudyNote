# SpringCloud学习



## Feign

> feign自带负载均衡配置项，和Ribbon一样可以自定义负载均衡模式，默认为轮询



## Config分布式配置中心

###  1) 配置配置中心

1. pom.xml依赖 

   ```xm
   <dependencies>
           <!-- springCloud Config -->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-config-server</artifactId>
           </dependency>
           <!-- 图形化监控 -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
           <!-- 熔断 -->
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-hystrix</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-eureka</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-starter-config</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-jetty</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
           </dependency>
           <!-- 热部署插件 -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>springloaded</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-devtools</artifactId>
           </dependency>
       </dependencies>
   ```

2. yml配置

   ```yaml
   server: 
     port: 3344 
     
   spring:
     application:
       name:  microservicecloud-config
     cloud:
       config:
         server:
           git:
             uri: https://github.com/wujingyong/springcloud_config.git #GitHub上面的git仓库名字
   ```

3. 在对应的git仓库中添加yml文件

4. SpringBootApplication中开启配置

   ```java
   @SpringBootApplication
   @EnableConfigServer
   public class Config_3344_StartSpringCloudApp {
       public static void main(String[] args) {
           SpringApplication.run(Config_3344_StartSpringCloudApp.class, args);
       }
   }
   ```

5. 验证配置

   ```text
   浏览器中输入   http://localhost:3344/master/microservicecloud-config-test.yml或
   http://localhost:3344/microservicecloud-config/test/master
   (详情百度配置规则)
   ```


### 2）配置eureka

1. 添加bootstrap.yml文件（系统文件，等级高于application.yml）

   ```yaml
   spring:
     cloud:
       config:
         name: microservicecloud-config-eureka-client     #需要从github上读取的资源名称，注意没有yml后缀名
         profile: test
         label: develop
         uri: http://localhost:3344      #SpringCloudConfig获取的服务地址
   ```

2. 配置application.yml，简单配置，没什么作用

   ```yml
   spring:
     application:
       name: microservicecloud-config-eureka-client
   ```

3. 配置SrpingBootApplication

   ```java
   @SpringBootApplication
   @EnableEurekaServer
   public class Config_Git_EurekaServerApplication {
       public static void main(String[] args) {
           SpringApplication.run(Config_Git_EurekaServerApplication.class,args);
       }
   }
   ```
