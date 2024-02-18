## DB initialiser
This inputs data **each time** application restarts so for a more permanent solution, we decided to input data straight into production db.
We can do that via intellij DB extension tool or any other DB tool and make an insert sql query

```java
@Configuration
public class DatabaseInitialiser {

    @Bean
    public CommandLineRunner initialiseDatabase(BankRepository repository) {
        return args -> {
            repository.save(new Bank("001", "한국은행"));
            repository.save(new Bank("002", "산업은행"));
            repository.save(new Bank("003", "기업은행"));
            repository.save(new Bank("004", "국민은행"));
            repository.save(new Bank("007", "수협은행"));
            repository.save(new Bank("008", "수출입은행"));
            repository.save(new Bank("011", "NH농협은행"));
            repository.save(new Bank("012", "농축협"));
            repository.save(new Bank("020", "우리은행"));
            repository.save(new Bank("023", "SC제일은행"));
            repository.save(new Bank("027", "한국씨티은행"));
            repository.save(new Bank("031", "대구은행"));
            repository.save(new Bank("032", "부산은행"));
            repository.save(new Bank("034", "광주은행"));
            repository.save(new Bank("035", "제주은행"));
            repository.save(new Bank("037", "전북은행"));
            repository.save(new Bank("039", "경남은행"));
            repository.save(new Bank("041", "우리카드"));
            repository.save(new Bank("045", "새마을금고"));
            repository.save(new Bank("048", "신협"));
            repository.save(new Bank("050", "저축은행"));
            repository.save(new Bank("052", "모간스탠리은행"));
            repository.save(new Bank("054", "HSBC은행"));
            repository.save(new Bank("055", "도이치은행"));
            repository.save(new Bank("057", "제이피모간체이스은행"));
            repository.save(new Bank("058", "미즈호은행"));
            repository.save(new Bank("059", "엠유에프지은행"));
            repository.save(new Bank("060", "BOA은행"));
            repository.save(new Bank("061", "비엔피파리바은행"));
            repository.save(new Bank("062", "중국공상은행"));
            repository.save(new Bank("063", "중국은행"));
            repository.save(new Bank("064", "산림조합중앙회"));
            repository.save(new Bank("065", "대화은행"));
            repository.save(new Bank("066", "교통은행"));
            repository.save(new Bank("067", "중국건설은행"));
            repository.save(new Bank("071", "우체국"));
            repository.save(new Bank("076", "신용보증기금"));
        };
    }

}

```
