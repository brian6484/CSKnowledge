## Gson
While there are multiple libraries to convert JSON files to Java objects and vice versa, I think Google's Json is simple

### Example data file
```
{
  "id": 2,
  "group_name": "group1",
  "student_list": [
    {
      "name": "Danny",
      "classroom": "1-2",
      "hobby": ["golf", "tennis", "squash"]
    },
    {
      "name": "Tony",
      "classroom": "2-5",
      "hobby": ["football", "baseball", "basketball"]
    }
  ]
}
```

### Declaring entity to be mapped 
This @SerializedName is really important. I kept naming it wrong when it should be "request" but I misnamed it as requestRR. FUg
```java
@ToString
@Getter
public class Group {
    @SerializedName("id")
    private int id;

    @SerializedName("group_name")
    private String name;

    @SerializedName("student_list")
    private List<Student> studentList;
}

@ToString
@Getter
public class Student {
    @SerializedName("name")
    private String name;

    @SerializedName("classroom")
    private String classRoom;

    @SerializedName("hobby")
    private List<String> hobbyList;
}
```
Lombok's @SerializedName specifies the name of the field to be mapped to or from JSON file

### Function
The method names are intuitive too. It is either toJson or fromJson.
deserialisation:
```java
public void parseNestJson() throws FileNotFoundException {
    FileReader fileReader = new FileReader("absolute file path");
    Gson gson = new Gson();

    Group group = gson.fromJson(fileReader, Group.class);
    System.out.println(group);
}
```

serialisation:
```java
String jsonResult = new Gson().toJson(json으로 바꾸고싶은 java객체);
```

If it is a list of group in the json file, it should be Group[].class
