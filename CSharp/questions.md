## Question 1: What is an immutable object? Why is string immutable?

### Short Answer

An immutable object is an object whose state (data) cannot be modified after it is created. If you try to change it, a new object is created in memory instead. In C#, `string` is immutable primarily to ensure **thread safety**, **security** (preventing accidental data corruption), and to enable **memory optimization** through mechanism called String Interning.

### Detailed Explanation

* **Definition**: Once an immutable object is instantiated, its internal fields cannot be changed.
* **String Behavior**: When you modify a string (e.g., using `.Replace()`, `+`, or `.ToUpper()`), the .NET runtime does not change the existing memory. Instead, it allocates a new block of memory for the modified string and returns a reference to that new object.
* **String Interning**: Because strings are immutable, the Common Language Runtime (CLR) can save memory by storing only one copy of duplicate string literals in a "String Intern Pool". Multiple variables can point to the same memory address safely because no one can modify it.
* **Hash Code Consistency**: Strings are frequently used as keys in HashTables and Dictionaries. If strings were mutable, changing the string content would change its HashCode, causing the item to get lost in the collection.
* **Security**: Strings carry sensitive data like file paths, database connection strings, and network addresses. Immutability ensures that once a string is validated (e.g., checking if a file path is safe), it cannot be changed maliciously or accidentally before it is used.
* **Thread Safety**: Since the data cannot change, multiple threads can read the same string simultaneously without risking race conditions or requiring locks.

### Code Example

```csharp
using System;

public class Program
{
    public static void Main()
    {
        // 1. Assign initial value
        string str1 = "Hello";
        
        // 2. Assign reference to str2
        string str2 = str1;
        
        Console.WriteLine($"Original str1: {str1}"); // Output: Hello
        Console.WriteLine($"Original str2: {str2}"); // Output: Hello
        
        // 3. Modify str1
        // This does NOT change the memory 'Hello' lives in.
        // It creates a new string "Hello World" and points str1 to it.
        str1 = str1 + " World";
        
        Console.WriteLine("--- After Modification ---");
        
        // str1 now points to a new object
        Console.WriteLine($"Modified str1: {str1}"); // Output: Hello World
        
        // str2 still points to the old object ("Hello")
        // If string were mutable, str2 would have also changed to "Hello World"
        Console.WriteLine($"str2 (Reference check): {str2}"); // Output: Hello
    }
}

```

### Real-World Use Case

* **Configuration & Security**: Passing database connection strings or API keys between layers. You want to guarantee that a lower-level function doesn't alter the credentials passed by a higher-level controller.
* **Dictionary Keys**: Using strings as keys in a `Dictionary<string, User>`. If the key changed after being added, the dictionary would fail to retrieve the user.
* **Caching**: Storing data in a cache where multiple threads read the same keys.

### When to Use

* **Value Objects**: When creating custom types representing values like `Address`, `Money`, or `Coordinate` where the identity is defined by the data, not a memory reference.
* **Multi-threading**: When you need high concurrency without the overhead of locks.
* **Hash Keys**: When the object will be used as a key in a Dictionary or HashSet.

### When NOT to Use

* **High-Frequency Updates**: Avoid using immutable objects (especially strings) inside tight loops where values change constantly.
* **Large Data Blobs**: If you have a massive text buffer that needs frequent small edits, immutability forces a full copy of the buffer for every edit, which is disastrous for performance.

### Performance / Memory Notes

* **Memory Pressure**: Frequent string manipulations (concatenation in loops) create thousands of temporary objects that must be garbage collected (GC). This is known as "String Thrashing."
* **Interning Benefit**: Immutable strings allow the CLR to de-duplicate literals. If you have the word "Status" 1000 times in your code, it may only occupy memory once.
* **Stack vs Heap**: Strings are reference types, so they live on the Heap, but the reference (pointer) lives on the Stack.

### Thread Safety

* **Yes, it is Thread-Safe**: Immutable objects are inherently thread-safe.
* Since the state cannot be modified, you do not need `lock` statements or `Monitor` to prevent read/write conflicts. Multiple threads can read the string "Config123" without fear that another thread will change it to "Config999" halfway through the read operation.

### Common Follow-Up Interview Questions

1. **If string is immutable, how does StringBuilder work?**
* *Answer*: `StringBuilder` is a mutable wrapper. It maintains an internal character array that is resized dynamically. It modifies the array in place until `.ToString()` is called.


2. **What is the String Intern Pool?**
* *Answer*: A table in memory where the CLR stores references to unique string literals to save space.


3. **Can you create a custom immutable class in C#?**
* *Answer*: Yes, by making the class `sealed`, declaring all fields `readonly`, and only initializing them via the constructor.


4. **Is `SecureString` immutable?**
* *Answer*: `SecureString` (now deprecated in .NET Core/5+) was mutable but encrypted in memory. It was designed to clear memory explicitly, unlike immutable strings which wait for GC.



### Mistakes Candidates Often Make

* **Loop Concatenation**: Writing `str += "x"` inside a `for` loop of 10,000 iterations. This creates 10,000 throw-away string objects.
* **Confusing `const` with Immutable**: Thinking `const` applies to the object instance. `const` applies to the variable reference at compile time, whereas immutability is a property of the object's memory behavior at runtime.
* **Modifying Parameters**: Assuming that passing a string into a method and changing it there affects the original variable (it doesn't, because strings behave like value semantics despite being reference types).

### Comparison: String vs StringBuilder

| Feature | String | StringBuilder |
| --- | --- | --- |
| **Mutability** | Immutable (cannot change) | Mutable (can change) |
| **Memory** | New object created on every change | Modifies internal buffer |
| **Thread Safety** | Thread-safe | Not thread-safe |
| **Best Use** | Storing static text, Keys, DTOs | Loops, heavy text manipulation |
| **Performance** | Slow for frequent updates | Fast for frequent updates |


**Next Step**:
Would you like me to explain **String Interning** in depth or provide a detailed implementation of a **Custom Immutable Class** in C#?

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/09872393-6111-4cc2-aaf2-96c4bf8df419" />

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/1841ccaa-5a1f-40dc-8ec5-3f88a7ba6571" />

