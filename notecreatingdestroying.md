# Riguardo la creazione e la distruzione di oggetti C++ #

Dati i problemi riscontrati in pair programming da me e Carmine riguardo la distruzione di oggetti in C++, ripropongo in questa sede alcune informazioni perchè tali problemi non abbiano più a ripetersi.

Da "Thinking in C++":

_C++ takes the approach that control of efficiency is the most important issue, so it gives the programmer a choice. For maximum runtime speed, the storage and lifetime can be determined while the program is being written, by placing the objects on the stack or in static storage. The stack is an area in memory that is used directly by the microprocessor to store data during program execution. Variables on the stack are sometimes called automatic or scoped variables. The static storage area is simply a fixed patch of memory that is allocated before the program begins to run. Using the stack or static storage area places a priority on the speed of storage allocation and release, which can be valuable in some situations. However, you sacrifice flexibility because you must know the exact quantity, lifetime, and type of objects while you’re writing the program. If you are trying to solve a more general problem, such as computer-aided design, warehouse management, or air-traffic control, this is too restrictive._

The second approach is to create objects dynamically in a pool of memory called the heap. In this approach you don’t know until runtime how many objects you need, what their lifetime is, or what their exact type is. Those decisions are made at the spur of the moment while the program is running. If you need a new object, you simply make it on the heap when you need it, using the new keyword. When you’re finished with the storage, you must release it using the delete keyword.

Because the storage is managed dynamically at runtime, the amount of time required to allocate storage on the heap is significantly longer than the time to create storage on the stack. (Creating storage on the stack is often a single microprocessor instruction to move the stack pointer down, and another to move it back up.) The dynamic approach makes the generally logical assumption that objects tend to be complicated, so the extra overhead of finding storage and releasing that storage will not have an important impact on the creation of an object. In addition, the greater flexibility is essential to solve general programming problems.

There’s another issue, however, and that’s the lifetime of an object. If you create an object on the stack or in static storage, the compiler determines how long the object lasts and can automatically destroy it. However, if you create it on the heap, the compiler has no knowledge of its lifetime. **In C++, the programmer must determine programmatically when to destroy the object, and then perform the destruction using the delete keyword**. As an alternative, the environment can provide a feature called a garbage collector that automatically discovers when an object is no longer in use and destroys it. Of course, writing programs using a garbage collector is much more convenient, but it requires that all applications must be able to tolerate the existence of the garbage collector and the overhead for garbage collection. This does not meet the design requirements of the C++ language and so it was not included, although third-party garbage collectors exist for C++_._


## Links utili ##

  * http://www.parashift.com/c++-faq-lite/freestore-mgmt.html#faq-16.11
  * http://msdn.microsoft.com/en-us/library/h6227113%28VS.80%29.aspx
  * http://www.cplusplus.com/doc/tutorial/dynamic/