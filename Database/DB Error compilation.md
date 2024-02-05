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

tbc

### Oracle sequence number doesnt exist

I checked if sequence exists but yea it existed. But the name of that sequence HAS TO MATCH with the @SequenceGenerator of my identity. The parameter - sequenceName needs to be exactly matching with the sequence name that I created in my Oracle DB

### Oracle trigger is invalid and failed revalidation 

Once I fixed the error above, I was faced with this error. I tried deleting and creating the trigger again but it 
didnt solve the issue. So the way to debug the error is 

```
SELECT * FROM user_errors WHERE type = 'TRIGGER' AND name = 'your_trigger_name';
```

For me, it showed as pls 00049 bad bind variable 'new'. Turns out, there was a spelling error when I was creating
the trigger, where I wrote :NEW:"POLICY_ID", but it should be a dot after NEW keyword like :NEW."POLICY_ID"
