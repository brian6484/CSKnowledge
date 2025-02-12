## @SQLDelete
Normally, calling delete entity literally deletes the entity.
But adding this annotation overrides this behaviour where instead of deleting, it updated the deleted_at column.

```java
@Entity
@Table(name = "post")
@Where(clause = "deleted_at IS NULL")  // Automatically applies this condition to queries
@SQLDelete(sql = "UPDATE post SET deleted_at = NOW() WHERE id = ?")
public class Post extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String content;
    
    private LocalDateTime deleted_at; // Soft delete column
}
```
