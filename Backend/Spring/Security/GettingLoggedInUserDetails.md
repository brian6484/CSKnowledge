## Steps
[ref](https://twoline.tistory.com/103)
1) Create a separate UserAdapater class that implements UserDetails and **has constructor of User**. This User class is the
user class that we wanna take fields from - like first name, mobile number, etc.
2) Overriding the methods must be done carefully. You should return user.fields() instead of null and true instead of false
for some methods. Put true for all if unsure.

Extra care for getAuthorities method. You should add the keyword **ROLE** in front like
```java
authorities.add((GrantedAuthority) () -> "ROLE_"+ ADMIN
```
3) Create a UserAccountService class that implements UserDetailsSerivce. Here since UserAdapter is an implementation of UserDetails,
you can return new UserAdapter(user).

Done
