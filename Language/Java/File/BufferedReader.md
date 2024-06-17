## BufferedReader
BufferedReader are functions that read with **buffer**. Normally, input that does not use a buffer (like Scanner) is sent straight to the program as soon as key is pressed. But buffer allows transmits each character to a buffer and delivers its content **in 1 go** when either its buffer is full or a newline character is met.

It can efficiently read characters, arrays, lines from an input stream. It extends Reader class and uses as **buffer** to store some data read
from incoming data stream. This reduces number of I/O operations by reading large chunks of data at once instead of each char at one time.

## advantage of using buffer
You might think it is faster to just send character as it is being typed instead of storing it in a buffer. But hard disk is much slower compared to RAM so continuous small writes will cause delay. Thus, reducing I/O operations helps its performance hugely.

## Constructor
```java
BufferedReader br = new BufferedReader(new InputStreamReader(inputSource))
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
