## @DynamicUpdate
It optimises SQL queries by updating **modified fields only** in a update query, rather than ALL fields.

```java
@Entity
@DynamicUpdate
@Table(name = "clothes")
public class Clothes extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String color;
    private String size;
}
```
