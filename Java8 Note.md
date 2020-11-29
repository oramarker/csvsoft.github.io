Java 8 

### JVM

* Object Header: Every object managed by the HotSpot JVM has a 12- or 16-byte header,

* Pointer Size: Normally, if you specify `-Xmx` less than 32 gigabytes, pointer size is 4 bytes; for bigger heaps, it's 8 bytes.

### Design Patterns

1. ### Singleton

   1.1 Eager initialization with final class level variable

   1.2 Eager initialization with static block and initialization exception handling.

   1.3 Lazy initialization thread safe with double check block.

   ```Java
   if(instance == null){ // Don't need to aquire the intrinsic lock every time to reduce race condition
     synchronized(this){
       if(instance == null){  // Once get , need to check null again.
         instance = new ThisClass();
       }
     }
   }
   ```

   1.4 Lazy initialization with inner static class.

## Laziy Initialization

```Java
if(instance == null){
  synchronized(this){
    if(instance == null){
      instance = new ThisClass();
    }
  }
}
```



### Collections

* HashMap
* TreeMap key must be Comparable, keys is ordered
* LinkedHashMap Reserves the insertion order

## Functional Interface

A functional interface is an interface with only one abstract method



### Optional

 The purpose of **Optional** is not to replace every single null reference in your code base but rather to **help you design better APIs** in which, just by reading the signature of a method, users can tell whether to expect an optional value and deal with it appropriately.



## JAVA NIO 2.0

### Buffer

**A `Buffer` object can be termed as container for a fixed amount of data. It acts as a holding tank, or temporary staging area, where data can be stored and later retrieved**

**The advantage of a `Buffer` class over a simple array is that it encapsulates data content and information about the data (i.e. meta data) into a single object.**



Buffer classes:

* IntBuffer
* ShortBuffer
* LongBuffer
* FloatBufer
* DoubleBuffer
* CharBuffer
* ByteBuffer
  * MappedByteBuffer

The **flip() method flips a buffer from a fill state, where data elements can be appended, to a drain state ready for elements to be read out**, which is equivalient to :

```Java
buffer.limit(buffer.position()).position(0)
```







