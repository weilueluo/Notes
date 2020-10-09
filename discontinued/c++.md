# C++ Notes

### Complier

Preprocess (process preprocess symbols) and translate the c++ code into machine code/object bytecode for each translation unit. A translation unit is a single source file, plus contents of any source file `#include` directly or indirectly by it, minus the code excluded by the preprocess statements like `#if, #else`.

### Linker

The linker basically resolve references that required to run the program and create a single executable, excluding DLLs (for Windows) and shared libraries (for Linux).

### Differences between `Struct` and `Class`

Technically the only difference is that by default visibility of member in struct is public while it is private for class. Struct merely exists for C compatibility and by convention it should be used when you just want to group some variables or POD.

> A Plain Old Data Structure in C++ is an aggregate class that contains only PODS as members, has no user-defined destructor, no user-defined copy assignment operator, and no nonstatic members of pointer-to-member type.

### Pointer and Reference

Pointer is the address of the variable while reference is a reference to the variable itself, it is used so that you do not have to write ugly code like `(*var)++` and can do `var++` instead when you want to change the data by reference.

### Static

- standalone variable: it means the variable only exists in its ***translation unit***.
- variable in function: it means changes to the variable will be preserved next time you call the same function. Note that the static variable in function in template class with different type is different.
- standalone function: it means the function only exists in its ***translation unit***, same meaning as variable.
- class function: static function that is the same for all instances, class function like Java.

### `#include`

A preprocess directive that will be replaced with the content of the including file.

### Interface

There is no interface in c++, just create a class consists of only virtual functions.

### Virtual / Polymorphism

Marking a function `virtual` allows subclass method to be called, even if the referencing type is superclass. If a function is not virtual but overridden, it will be called according to the type of reference not the type of object. Note that a virtual function is far more expensive than a normal function call because internally it uses a look up table to find the required method.

### const / mutable

`const type* const ptr = object`: first `const` means you cannot change the content of the pointer; second const means you cannot reassign `ptr` to another `object`.

`const int* const getX() const {return m_X;}` in class: first `const` means the returned `m_X`'s content cannot be modified; the second `const` means the `m_X`'s referencing object cannot be changed; the third `const` means the function does not modify the member of the class.

`mutable` marks a variable so that it can be modified in a `const` function. Note that this keyword should only be used when a library designer knows that changing that variable does not result in unexpected behavior of user of the library, such as incrementing an internal reference count.

### Initializer

```c++
class Entity {
	float x, y;
	Entity(float x, float y) : x(x), y(y) {}
};
```

Just use them everywhere, because if you initialize in the `{}`, it will initialize the member variables twice, once when the constructor is called, another when you overwrite it in constructor.

### Copy Constructor

```c++
Type a ();
Type b = a;
```

The second line of the code above does an shallow copy of `a` and assign to `b`, it simply means the object `a` 's memory is copied to form the object `b`. Sometimes this causes unexpected behavior. For example if `a` stores an id then `b` will contains the same id, if id is meant to be unique in each instance then bad things happen.

The solution is to write a copy constructor for the `Type` class. It is a method that gets called when the copying like the second line above happens.

### Dynamic Array

If the array holds pointer to the actual object, then it will be faster when expanding the array as we just need to copy the pointer not the content of the object to new location; if the array holds the actual object then it will be faster for iterating because the memory is inline.

When iterating on array of object by range, use reference `&` to avoid copying the object.

```c++
for (const Type& item: array) {
	std::cout << item << std::endl;
}
```

Optimization:

- Reserve enough memory for the size of array using `reserve` before pushing anything into the vector.
- Use `emplace_back` instead of `push_back` to push new object to avoid constructing and copy the object into the array.