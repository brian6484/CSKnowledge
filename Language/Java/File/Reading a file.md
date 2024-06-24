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

## Various ways
```java
public class FileReadUsingJava {
    public static void main(String[] args) throws Exception {

        URL resource = FileReadUsingJava.class.getClassLoader().getResource("test.txt");
        List<String> strings = Files.readAllLines(Paths.get(resource.toURI()));
        strings.forEach(System.out::println);

        //InputStream
        InputStream inputStream = FileReadUsingJava.class.getClassLoader().getResource("test.txt").openStream();
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream));
        bufferedReader.lines()
                .forEach(System.out::println);

        //File로 읽기
        ClassLoader classLoader = FileReadUsingJava.class.getClassLoader();
        File file = new File(classLoader.getResource("test.txt").getFile());
        BufferedReader bufferedReader1 = new BufferedReader(new FileReader(file));
        bufferedReader1.lines()
                .forEach(System.out::println);

        //Paths 사용
        String path = FileReadUsingJava.class.getClassLoader().getResource("test.txt").getPath();
        Stream<String> lines = Files.lines(Paths.get(path));
        lines.forEach(System.out::println);
    }
}
```
