## Method 1
If your file is in the resources folder, you can use .getClassLoader() to scan src/main/resources folder. 
.getResource("file_name_that_you_r_looking_for") is invoked after getClassLoader().

```java
Path path = Paths.get(getClass().getClassLoader().getResource("text.txt").toURI())
try (BufferedReader br = new BufferedReader(new FileReader(path.toString()) {
// bla bla
}
```

## Method 2
You can straightaway use ClassPathResource, which reads from src/main/resoruces/.
```java
ClassPathResource resource = new ClassPathResource("data.json");
try 
```
