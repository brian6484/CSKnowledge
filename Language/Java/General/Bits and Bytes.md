## Intro
So 8 bits make up 1 byte that is basic knowledge. In java, 1 byte is a 8-bit **signed** two's complement integer. So it can
store values from -128 to 127. We can use a byte[] array for several purposes and each byte in that array is capable of holding
values from that range (-128 to 127). To convert whatever data to a byte array, we normally use methods like .getBytes() or
.readAllBytes() as seen below.

## binary data
Raw binary data like images can be stored in binary array. For example reading a file

```java
String inputFilePath = "input.bin";
String outputFilePath = "output.bin";

try {
    // Read binary data from a file into a byte array
    FileInputStream fis = new FileInputStream(inputFilePath);
    byte[] byteArray = fis.readAllBytes();
    fis.close();
    
    // Print byte array
    System.out.println("Read byte array: " + java.util.Arrays.toString(byteArray));
    
    // Write the byte array to another file
    FileOutputStream fos = new FileOutputStream(outputFilePath);
    fos.write(byteArray);
    fos.close();
    
    System.out.println("Binary data written to " + outputFilePath);
} catch (IOException e) {
    e.printStackTrace();
}
```

## text data
A simple string can also be converted into a byte array. Each character can be convereted to 1 byte using **getBytes() and
UTF-8 encoding**. For example, a simple string like "hello" contains only ASCII characters so each char is converted to 1 byte
with range 0 to 127.

```java
String text = "Hello, World!";

// Convert string to byte array
byte[] byteArray = text.getBytes(java.nio.charset.StandardCharsets.UTF_8);

// Print byte array
System.out.println("Byte array: " + java.util.Arrays.toString(byteArray));

// Convert byte array back to string
String newText = new String(byteArray, java.nio.charset.StandardCharsets.UTF_8);
System.out.println("Converted back to string: " + newText);

// Print the byte values
for (byte b : byteArray) {
    System.out.print(b + " ");
}
```

the output is like this cuz H is 72, e is 101, etc.
```
72 101 108 108 111 44 32 87 111 114 108 100 33
```

## network data
We need to get data via InputStream and OutputStream
```java
String serverName = "localhost";
int port = 6066;
try {
    System.out.println("Connecting to " + serverName + " on port " + port);
    Socket client = new Socket(serverName, port);
    
    System.out.println("Just connected to " + client.getRemoteSocketAddress());
    
    OutputStream outToServer = client.getOutputStream();
    DataOutputStream out = new DataOutputStream(outToServer);
    
    String message = "Hello from client";
    byte[] messageBytes = message.getBytes();
    out.write(messageBytes);
    
    InputStream inFromServer = client.getInputStream();
    DataInputStream in = new DataInputStream(inFromServer);
    
    byte[] buffer = new byte[1024];
    int bytesRead = in.read(buffer);
    
    String response = new String(buffer, 0, bytesRead);
    System.out.println("Server says: " + response);
    
    client.close();
} catch (IOException e) {
    e.printStackTrace();
}
}
```

## serialisation 
We can convert java objects to a *byte stream* via serialisation that can be sent over a network or stored in a file. We can
revert the byte stream back to objects via deserialistion.


