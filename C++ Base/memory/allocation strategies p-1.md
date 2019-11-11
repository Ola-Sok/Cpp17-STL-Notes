There a lot of different strategies for allocating memory. Programming languages like Python or Java request their memory from the heap at runtime. Of course, C or C++ also has a heap but prefers the stack. But these are by far not so only strategies for allocating memory. You can preallocate memory at the start time of the program as a fixed block or as a pool of memory blocks. This preallocated memory can afterwards be used at the runtime of your program. But the key question is—What are the pros and cons of the various strategies to allocate memory?

At first, I want to present four typical memory strategies.

# Strategies for the allocation of memory
### Dynamic allocation
Dynamic allocation also called variable allocation is known to each programmer. Operations like new in C++ or malloc in C request the memory when needed. In contrary, calls like delete in C++ or free in C release the memory when not needed anymore.
```C++
 int* myHeapInt= new int(5);
 delete myHeapInt;
```
The subtle difference in the implementation in different languages is, whether the memory is automatically or needs to be manually released. Languages like Java or Python have general garbage collection, C++ or C in contrary not. (Only for clarification: That is of course not totally true, because we have the Boehm-Demers-Weiser conservative garbage collector that can be used as a garbage collecting replacement for C malloc or C++ new. Here are details: https://www.hboehm.info/gc/).

Dynamic memory management has a lot of pros. You can automatically adjust the memory management to your needs. The cons are that there is always the danger of memory fragmentation. In addition, a memory allocation can fail or take too much time. Therefore, a lot of reasons speak against dynamic memory allocation in highly safety-critical software that requires deterministic timing behaviour.

Smart Pointers in C++ manage their dynamic memory by objects on the stack.

### Stack allocation
Stack allocation is also called memory discard. The key idea of stack allocation is that the objects are created in a temporary scope and are immediately freed, if the objects goes out of scope. Therefore, the C++ runtime takes care on the lifetime of the objects. Scope are typically areas like functions, objects, or loops. But you can also create artificial scopes with curly braces.  
```C++
{
  int myStackInt(5);
}
```

A very beautiful example for stack allocation are the smart pointers std::unique_ptr and std::shared_ptr. Both are created on the stack in order to take care of objects that are created on the heap.

But what are the benefits of stack allocation? At first, the memory management is as easy as possible because the C++ runtime manages it automatically. In addition, the timing behaviour of memory allocation on the stack is totally deterministic. But there are big disadvantage. At one hand, the stack is smaller than the heap; at the other hand, the objects should often outlife its scope and C++ supports no dynamic memory allocation on the stack.

### Static allocation
Most of the times static allocation is called fixed allocation. The key idea is that at runtime required memory will be request at the start time  and released at the shutdown time of the program. The general assumption is here that you can precalculate the memory requirements at runtime.
```C++
char* memory= new char[sizeof(Account)];
Account* a= new(memory) Account;

char* memory2= new char[5*sizeof(Account)];
Account* b= new(memory2) Account[5];
```

The objects a and b in this example are constructed in the preallocated memory of memory and memory2.

What are the pros and cons of static memory allocation? In hard real-time driven applications, in which the timing behaviour of dynamic memory is no option, static allocation is often used. In addition, the fragmentation of memory is minimal. But of course, there are pros and cons. Often it is not possible to precalculate the memory requirements of the application upfront. Sometimes your application requires extraordinary memory at runtime. That may be an argument against static memory allocation. Of course, the start time of your program is longer.

### Memory pool
Memory pool also called pooled allocation combines the predictability of static memory allocation with the flexibility of dynamic memory allocation. Similar to the static allocation you request the memory at the start time of your program. In opposite to the static allocation you request a pool of objects. At runtime the application uses this pool of objects and return them to the pool. If you have more the one typical size of objects, it will make sense to create more than one memory pool.

What are the pros and cons? The pros are quite similar to the pros of static memory allocation. At one hand, there is no fragmentation of memory; on the other hand, you have predictable timing behaviour of the memory allocation and memory deallocation. But there is one big advantage of memory pool allocation to static allocation. You have more flexibility. This for two reasons. The pools can have different sizes and you can return memory back to the pool. The cons of memory pools it  that this technique is quite challenging to implement.

## What's next?
Sometimes, I envy Python or Java. They use dynamic memory allocation combined with garbage collection and all is fine. All? All the four presented techniques are in use in C or C++ and offer a lot. In the next post I will have a closer look at the difference of the four techniques. I'm in particular interested in the predictability, scalability, internal and external fragmentation, and memory exhaustion.

**Source:**
https://www.modernescpp.com/index.php/strategies-for-the-allocation-of-memory
