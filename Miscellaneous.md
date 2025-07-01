## Internal working of ArrayList
- ArrayList uses array in the background
- Initially an empty array is created(Before java 8, on initialization the size of the array used to be 10) and when we add the first element the array size changes to 10
- When we exceed the current size, then the size is increased to `1.5x + 1`

## Internal working of HashSet
- HashSet is backed by a HashMap internally.
- When we add an element to a HashSet, it internally calls:
     - map.put(element, PRESENT);
     - Where `private static final Object PRESENT = new Object();`
 
## How to make a class Immutable
```java
package org.example;

// Immutable class
final class Immutable { // Class needs to be final so that cannot be extended and overridden.
    private final int id; // Private makes in-accessible out of class, final makes the unable to change.
    private final String name;
    public Immutable(int id, String name) {
        this.id = id;
        this.name = name;
    }
    // Don't create a setter so the data members can only be assigned while
    // creating the object and cannot be changed with the setter.
    int getId() {
        return id;
    }
    String getName() {
        return name;
    }
}

public class Main {
    public static void main(String[] args) {
        Immutable obj = new Immutable(1, "yash");
        int id1 = obj.getId();
        String name1 = obj.getName();
        System.out.println(id1);
        System.out.println(name1);

        // These assignments don't affect the original object
        id1 = 10;
        name1 = "miks";

        System.out.println(obj.getId() + obj.getName());
    }
}
```
## Note: In the case of Set, List, or Date as data members, a deep copy should be made inside the constructor. This is because these objects are mutable, and without copying, their state could be modified from outside the class even after being assigned.

## ✅ Thread-Safe Singleton Class in Java (with Double-Checked Locking)

```java
class Singleton {

    // Volatile ensures visibility of changes to instance across threads.
    private static volatile Singleton instance;

    // Private constructor prevents instantiation from outside the class.
    private Singleton() {}

    // Public method to provide access to the Singleton instance.
    public static Singleton getInstance() {
        if (instance == null) { // First check (no lock)
            synchronized (Singleton.class) {
                if (instance == null) { // Second check (with lock)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

public class Main {
    public static void main(String[] args) {
        Singleton obj = Singleton.getInstance(); // Accessing singleton instance
        System.out.println(obj); // Verifying singleton by printing the reference
    }
}
```
------------------------
# N+1 Problem in Hibernate/JPA

## ❓ What is the N+1 Problem?

- It occurs when fetching a parent entity and lazily loading its child entities.
- 1 query fetches the parent entities.
- N additional queries are fired — one for each parent to fetch its children.
### ➕ Total Queries = N (parents) + 1 = **N+1 Queries**

## 📦 Example

### Entities:
- `Customer` (parent)
- `Address` (child) — One-to-Many relationship

### Code:
```java
List<Customer> customers = customerRepo.findAll();
for (Customer c : customers) {
    List<Address> addresses = c.getAddresses(); // Triggers query per customer
}
```
In the above example 1 query will fetch all the customers and N queries will fetch their respectivce address**es**

### soluton
1. @EntityGraph
```java
@EntityGraph(attributePaths = {"addresses"})
List<Customer> findAll();
```
2. JOIN FETCH
```java
@Query("SELECT c FROM Customer c JOIN FETCH c.addresses")
List<Customer> findAllWithAddresses();
```


