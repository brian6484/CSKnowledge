

## 1 line similarity
They are both interfaces that are used for **object sorting**, not basic type sorting.

## 1 line diff
Comparable gives a comparison standard **inside the object** whereas comparator compares 2 objects **outside the object** 
(so it doesnt modify class like comparable) and serves as a comparsion logic. Note both **dont sort** but it is
just a comparison logic. It is the **utility class like Collections and Arrays that sort** based on the Comparable and Comparator's comparison
logic.

## Intro
[very good ref](https://komas.tistory.com/37) 

When you have tried implementing sort, when we compare 2 elements with comparison operators like < or >, we swap based
on the boolean result (ture/false) via that comparsion operator. But we cannot use < or > for **object comparsion** cuz
they are all reference values. So we need Comparable/Comparator to provide the sorting logic on object's field and 
strictly speaking, it is the **utility class** like collections and arrays that do the **swapping and sorting**.

## What you need to know before using Comparable and Comparator
Lets see how we sort a simple array of [3,5]. When we subtract those 2 values via 3-5, we get -2. Since the result is
negative, we know latter is larger than former, which shows an ascending order. So we leave it be.

But if the array is [5,3], and we compare those 2 values, we get 5-3 =2 wich is positive. This isnt ascending order so 
we swap those 2 values.

See the pattern? **If result of subtraction is negative, latter is bigger than former, so ascending order is right**.
Else, swap. It is going to be similar when comparing object's field - be it string or any other data types.

## Comparable
Remember comparable provides the comparison logic **inside the object**? So the object class needs to **implement Comparable and
override compareTo method**.

```java
public class Person implements Comparable<Person> {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Person other) {
        //provides the compison logic
        //Integer.compare does this.age-other.age and if result is negative, it gives -1 
        return Integer.compare(this.age, other.age);
    }

    // Getters and setters

    @Override
    public String toString() {
        return name + ": " + age;
    }

    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));

        //it is the utility class that sorts based on the comparison logic
        Collections.sort(people);

        for (Person person : people) {
            System.out.println(person);
        }
    }
}

```

## Comparator (more common)
The abstract method of Comparator is compare(). Notice we pass 2 parameters to this method. Thats cuz compare() is not comparing
itself with something else but it compares 2 objects by getting them as arguments **from outside**. Also, we dont need to modify the 
class like before.


```java
import java.util.*;

public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return name + ": " + age;
    }

    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));

        // Sort by name using lambda expression
        people.sort((p1, p2) -> p1.getName().compareTo(p2.getName()));

        //or using anonymus inner class like
        Collections.sort(people, new Comparator<Person>() {
            @Override
            public int compare(Person p1, Person p2) {
                return Integer.compare(p1.age, p2.age);
            }
        });

        System.out.println("Sorted by name:");
        for (Person person : people) {
            System.out.println(person);
        }

        // Sort by age using lambda expression
        people.sort((p1, p2) -> Integer.compare(p1.getAge(), p2.getAge()));

        //sort by Comparator's in-house comparison methods like Comparator.comparing
        people.sort(Comparator.comparingInt(Person::getAge));

        System.out.println("Sorted by age:");
        for (Person person : people) {
            System.out.println(person);
        }
    }
}

```

## Ways to compare
1) anonymous inner class - when you just need to use once and not think about reusability
2) lambda class - when you need to reuse
3) method reference (that double colon thing but i dont really find it intuitive)
4) using Compartor's in-house methods

## Multiple ways to sort
We can define multiple ways to sort via **thenComparing** like python's key =lambda x:x[0],-x[1] like this. 

example using method reference for comparison logic and multiple sorting logic via **thenComparing**.
```java
import java.time.LocalDateTime;
import java.util.*;
import java.util.stream.Collectors;

public class OutputPicker {
    private String id;
    private LocalDateTime activeSince;

    public OutputPicker(String id, LocalDateTime activeSince) {
        this.id = id;
        this.activeSince = activeSince;
    }

    public String getId() {
        return id;
    }

    public LocalDateTime getActiveSince() {
        return activeSince;
    }

    @Override
    public String toString() {
        return "OutputPicker{" +
                "id='" + id + '\'' +
                ", activeSince=" + activeSince +
                '}';
    }

    private static Comparator<OutputPicker> getComparator() {
        return Comparator.comparing(OutputPicker::getActiveSince)
                         .thenComparing(OutputPicker::getId);
    }

    public static void main(String[] args) {
        List<OutputPicker> pickers = new ArrayList<>();
        pickers.add(new OutputPicker("B", LocalDateTime.of(2021, 5, 15, 10, 0)));
        pickers.add(new OutputPicker("A", LocalDateTime.of(2021, 5, 15, 10, 0)));
        pickers.add(new OutputPicker("C", LocalDateTime.of(2020, 5, 15, 10, 0)));

        // Sort using the comparator
        Collections.sort(pickers, getComparator());

        // Or you can use the stream API to sort and collect
        List<OutputPicker> sortedPickers = pickers.stream()
                                                  .sorted(getComparator())
                                                  .collect(Collectors.toList());

        sortedPickers.forEach(System.out::println);
    }
}

```

## Reverse order (descending instead of ascending order)
We do it like this using reversed() at the end

```java
private static Comparator<OutputPicker> getComparatorDescending() {
    return Comparator.comparing(OutputPicker::getActiveSince).reversed()
                     .thenComparing(OutputPicker::getId).reversed();
}
```

