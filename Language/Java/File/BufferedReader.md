## BufferedReader
It can efficiently read characters, arrays, lines from an input stream. It extends Reader class and uses as **buffer** to store some data read
from incoming data stream. This reduces number of I/O operations by reading large chunks of data at once instead of each char at one time.

## Constructor
```java
BufferedReader br = new BufferedReader(new InputStreamReader(source))
```
InputStreamReader's constructor parameter can take any class type that *extends* InputStream to offer flexibility in reading from a variety
of data sources.


example:
```java
//file
try (BufferedReader br = new BufferedReader(new FileReader(filePath))) {
            String line;
            // Read each line from the file
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
}

//general InputStream (that can be file or tcp stream or any data stream)
try (BufferedReader br = new BufferedReader(new InputStreamReader(source))) {
            String line;
            while ((line= br.readLine()) != null) {
                System.out.println(order);
            }

        }
```
