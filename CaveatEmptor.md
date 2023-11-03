# CaveatEmptor

## Domain logic
* Each item is gonna be auctioned just once so we don't need to make another entity called auction item entity. Instead, we create a Bid
entity that is associated with this Item entity.
*  Address info of User will be stored in another separate class (i.e. db table). And User is gonna have not just 1 but 3 Address - Home, Billing and Shipping.
*  User can also have many billing methods so this BillingDetails class will be made abstract to allow its subclasses to represent different billing methods
like CreditCard, BankAccount (open for future extension ACID)
* Category might nest Category within itself like a recursive association. Each Item has at least one Category and a single Category can have multiple child categories
but max 1 parent.

![image](https://github.com/brian6484/StudyNote/assets/56388433/3434357e-a4d9-4f97-b0a2-d50fd927444e)

## User
rmb Hibernate needs noargrsconstructor for each persistent class for performance optimisation - lazy loading and when invoking lifcycle events like entity listeners
or interceptors

configure entity,jparepo and yml db details
```java
@Entity
@Table(name = "USERS")
public class User {
    @Id
    @GeneratedValue
    private Long id;
    private String username;
    private LocalDate registrationDate;
    private String email;
    private int level;
    private boolean active;

    public User() {
    }

    public User(String username) {
        this.username = username;
    }

    public User(String username, LocalDate registrationDate) {
        this.username = username;
        this.registrationDate = registrationDate;
    }

    // Getters and setters
}
```

