## DB Errors
### Mysql3 java.lang.ClassNotFoundException: com.mysql.cj.jdbc.Driver
Referred to [this](https://velog.io/@nasubeeni/Spring%EC%8A%A4%ED%94%84%EB%A7%81-java.lang.ClassNotFoundException-com.mysql.cj.jdbc.Driver)

So after checking that I have imported the correct dependency in build.gradle and still there is this annoying red highlight in yml file at
driver-class-name property, I googled for a solution.

Apparently it is caused when mysql jdbc driver is not on the classpath. More about jdbc and jdbc driver [here](https://github.com/brian6484/CSKnowledge/blob/main/Database/JDBC%20%26%20JDBC%20Driver.md)

So anyway a brief note on classpath is that it is a parameter that tells the JVM and Java runtime environment where to find classes that
user coded and libraries in the project. When a Java program is executed, JVM looks for classes in the directories and JAR files specified
in the classpath.

So since our mysql jar file is not found in our classpath, we download mysql-connector-j jar file first. Then, we open Project Structure ->
Modules. In our main module, go to `Dependencies`, click on + button and add 1.Jar or Directories. Then add your mysql-connector-j jar file and
Apply the changes. Don't forget to Invalidate cache and Restart!

### Mysql3 Unable to resolve name [org.hibernate.dialect.MySQL5Dialect] as strategy
Referred to [this](https://velog.io/@yesue/SpringBoot-Failed-to-initialize-JPA-EntityManagerFactory-Unable-to-create-requested-service-...-due-to-Unable-to-resolve-name-org.hibernate.dialect.MySQL5InnoDBDialect-as-strategy-...-%EC%97%90%EB%9F%AC)

Just change the dialect in yml file to 
```
org.hibernate.dialect.MySQL8Dialect
```

