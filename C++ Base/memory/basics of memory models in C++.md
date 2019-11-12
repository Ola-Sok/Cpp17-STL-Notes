I will try to clear the confusion as much as I can. First of all, learn to separate low-level memory model concepts (stack, heap) from C++-level memory concepts. In the world of C++, `stack` and `heap` do not mean anything remotely resembling stack or heap in low-level model. 

# Low-level memory model
First, let's talk about low-level memory model. Traditionally, memory is split between 'stack' and 'heap' memory, which I cover next.

## Stack
The stack is managed by so-called 'stack pointer' CPU register - which always indicates the top of the stack and goes continuously from high-level memory addresses to low-level memory addresses. Since the top of the stack is always pointed to by the register (you may know the last-in first-out (LIFO) principle), there is no need for any real memory management associated with the stack - when you need more memory, the value stored in the stack pointer decreases automatically while you allocate new memory on the stack. When you no longer need the memory and your program frees it, the value stored in the stack pointer increases and the memory on the stack that was just occupied by your program is able to be allocated again. Obviously, the problem with that approach is that it is not sustainable - you can not free (or allocate) memory within the block. So if you allocated memory for 3 objects, A, B, C and you no longer need the object B, there is no way for memory occupied by B to be freed - single stack pointer simply does not have capabilities to do so.

That limits the usage of the stack memory to the cases of 'close-reach', short-lived objects - when you know that you do not need to selectively free any memory associated with objects allocated within this scope, and can simply free all of them soon enough. This make stack memory an ideal storage for a variables defined within a function - all of them are freed together when the function exits. What's even better is that compiler can do this automatically for you - you do not have to explicitly tell the compiler when to free the memory for each variable - it is going to be freed automatically once the code execution left its scope.

It is also worth noting that stack allocation and freeing are uberfast - they only require a single register arithmetic operation.

However, as I said before, stack has limitations. Heap memory is here to overcome those - and is described next.
## Heap
Unlike the stack (which is only managed by simple register) heap memory is supported by complex structures and logic. You can request memory from the heap, and you can return memory back to the heap, and you can do it independently for every object. So, going back to my original example, when you requested memory for objects A, B and C (all the same size), and no longer need object B, you can return memory for B and still retain A and C. If you need to create another object, D, of the same size as those before and ask for the memory for it, heap can give you memory you returned from B. While it is not guaranteed (heap algorithms are very complex) this is a good enough simplification. 

Unlike stack memory, managing heap memory has it's costs, which are actually comparatively quite high (especially in multithreaded environment). That's why heap memory should not be used if one can help it, but this is a huge topic on it's own, which I am not going to dwell on now.

One very important property of the heap memory is that it has to be explicitly managed by the user. You need to request memory when you need it, give it back when you no longer need it, and never use the memory you've given back. Failure to observe those rules would either make your program leak memory - that is, consume memory without giving it back, which would cause the program to eventually run out of memory - in case you do not give memory back; or cause the program to behave incorrectly (if you use the memory before requesting or after giving back) as you will be accessing memory which is not yours. 

# C/C++ memory model
For better or worse, C/C++ shield the programmer from those low-level memory concepts. Instead, the language specifies that every variable lives in a certain type of storage, and it's lifetime is defined by the storage type. There are 3 types of storage, outlined below.

## Automatic storage
This storage is managed by the compiler 'automatically' (hence the name) and does not require the programmer to do anything about it. An example of automatic variable is one defined inside a function body:
```C++
    void foo() {
       int a;
    }
```
`a` here is automatic. You do not need to worry about allocating memory for it or cleaning it when it is no longer needed, and compiler guarantee you that it will be there when you enter function foo(), and will no longer be there when you exit foo(). While it **might** be allocated on the stack, there is absolutely no guarantee about it - it might as well be put in the register. Registers are so much faster than any memory, so compilers will make use of them whenever they can.

## Static storage
Variables put in static storage live until the program exits. Again, developer does not need to worry about their lifetime, or cleaning up the memory - the memory will be cleaned up after program exits, and not before. An example of static duration variable is a variable, defined outside of any function (global variable), static local variables of the function, and static members of the class. In the code below var1, var2 and var3 are all variables within static storage:

Code (with some inline comments):
```C++
    int var1;
    
    void foo() {
        static int var2;
    }
    
    class A {
       static int var3;
    }
```
## Dynamic storage
Dynamic storage variables are controlled by developer. When you need them, you request the memory (usually with `malloc` in C or `new` in C++) and you must give it back when you no longer need it (with `free` in C, `delete` in C++). As a developer, you should be paying all attention in how you allocate, use and delete those, and make sure the sequence is never broken. Failure to observe the sequence is a single major cause of all the great program bugs making the news :). Luckily, C++ has special features and classes for you that simplify this task, but if you develop in C, you are on your own. In the example below, memory to where var4 points is dynamically allocated.

Code:
```C++
    void foo() {
       int* var4;
       // Here is the major source of confusion. var4 itself is **automatic**
       // you do not need to allocate or free var4 memory, so you can use it
       // like this:
       var4 = NULL; // Not an error!!!
       // However, you can't use the memory var4 points to yet!
       // Following line would cause incorrect behavior of the program:
       // *var4 = 42; // NEVER EVER!!!
       // Instead, you need to allocate the memory first (let's assume, we are in C++
       var4 = new int();
       // Now the memory was allocated, we can use it
       *var4 = 42; // Correct!
       // we no longer need this memory, so let's free it:
       delete var4;
       // This did not change var4 itself (unless there is a special case)
       // so technically, it still points to the memory which was former 
       // belonging to you. But the memory is no longer yours!!!
       // you can't read or write it!
       // Following code is bad-bad-bad:
       // int x = *var4; // NEVER EVER! 
    }
```
As you've seen, using dynamic memory comes with most caution and warning signs. This is why in C++ there are special facilities to make this easier, and no one is expected to write the code I've written above.


##### Source: https://stackoverflow.com/a/34549788/9548903


#### Next topic: proper memory management in C++.
