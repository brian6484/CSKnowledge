read the theory and class usage first

very good [ref](https://st-lab.tistory.com/153)
## Generic meethod
```java
// 제네릭 클래스
class ClassName<E> {
    private E element;  // 제네릭 타입 변수

    // 제네릭 파라미터 메소드
    void set(E element) {
        this.element = element;
    }

    // 제네릭 타입 반환 메소드
    E get() {
        return element;
    }

    // 제네릭 메소드
    <T> T genericMethod(T o) {
        return o;
    }
}

// 메인 클래스 
public class Main {
    public static void main(String[] args) {
        ClassName<String> a = new ClassName<String>();
        ClassName<Integer> b = new ClassName<Integer>();

        a.set("10");
        b.set(10);

        System.out.println("a data : " + a.get());
        // 반환된 변수의 타입 출력 
        System.out.println("a E Type : " + a.get().getClass().getName());

        System.out.println();
        System.out.println("b data : " + b.get());
        // 반환된 변수의 타입 출력 
        System.out.println("b E Type : " + b.get().getClass().getName());
        System.out.println();

        // 제네릭 메소드 Integer
        System.out.println("<T> returnType : " + a.genericMethod(3).getClass().getName());

        // 제네릭 메소드 String
        System.out.println("<T> returnType : " + a.genericMethod("ABCD").getClass().getName());

        // 제네릭 메소드 ClassName b
        System.out.println("<T> returnType : " + a.genericMethod(b).getClass().getName());
    }
}
```

## STATIC generic method (very tricky)
The error occurs because static methods in Java do not have access to the type parameters (E) of their enclosing class (ClassName<E>). They operate independently of any object instance and therefore cannot rely on instance-specific type parameters. To use generics in static methods, you either define a new type parameter for the method or explicitly specify the type when invoking the method. This ensures that the static method operates within the bounds of Java's static context while still leveraging the benefits of generics.
