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
You can straightaway use ClassPathResource, **which reads from src/main/resoruces/.**
I need to emphasise that it already includes src/main/resoruces/, so your declared directory should be
```
## correct
APPLE_KEY_DIR=static/apple/sdssd.p8

## wrong
APPLE_KEY_DIR=src/main/resources/static/apple/sdssd.p8
```


```java
ClassPathResource resource = new ClassPathResource("data.json");
try 
```

## Various ways
```java
import java.io.*;
import java.net.URL;
import java.nio.file.*;
import java.util.List;
import java.util.stream.Stream;

public class FileReadUsingJava {

    public static void main(String[] args) throws Exception {
        FileReadUsingJava fileReader = new FileReadUsingJava();

        // Example: Reading test.txt
        fileReader.readFile("test.txt");

        // Example: Reading another file, e.g., example.json
        fileReader.readFile("example.json");
    }

    public void readFile(String fileName) throws IOException, URISyntaxException {
        // Get the URL of the resource file
        Path path = Paths.get(getClass().getClassLoader().getResource(fileName).toURI())

        if (resourceUrl == null) {
            System.out.println("File not found: " + fileName);
            return;
        }

        try (BufferedReader br = new BufferedReader(new FileReader(path.toString()) {
        // bla bla
        }

        // Using Files.readAllLines
        System.out.println("Using Files.readAllLines:");
        List<String> strings = Files.readAllLines(Paths.get(resourceUrl.toURI()));
        strings.forEach(System.out::println);

        // Using InputStream and BufferedReader
        System.out.println("\nUsing InputStream and BufferedReader:");
        try (InputStream inputStream = resourceUrl.openStream();
             BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(inputStream))) {
            bufferedReader.lines().forEach(System.out::println);
        }

        // Using File
        System.out.println("\nUsing File:");
        File file = new File(resourceUrl.toURI());
        try (BufferedReader bufferedReader1 = new BufferedReader(new FileReader(file))) {
            bufferedReader1.lines().forEach(System.out::println);
        }

        // Using Paths
        System.out.println("\nUsing Paths:");
        String path = resourceUrl.getPath();
        try (Stream<String> lines = Files.lines(Paths.get(path))) {
            lines.forEach(System.out::println);
        }
    }
}

```
