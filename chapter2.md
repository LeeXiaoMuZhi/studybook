
#iOS修饰词总结

<em> 原文档关于自动引用说明：Automatic Reference Counting (ARC) is a compiler feature that provides automatic memory management of Objective-C objects. Rather than having to think about retain and release operations, ARC allows you to concentrate on the interesting code, the object graphs, and the relationships between objects in your application.
        
翻译过来就是：Automatic Reference Counting (ARC)是一个编译器的特性，提供了对iOS对象的自动内存管理,ARC在编译期间自动在适当的地方添加ObjC对象的retain和release操作代码，而不需要我们关心。
        
ARC在编译期间，根据Objective-C对象的存活周期，在适当的位置添加retain和release代码。从概念上讲，ARC与手动引用计数内存管理遵循同样的内存管理规则，但是ARC也无法防止循环强引用。
        
ARC还引入了新的修饰符来修饰变量和声明属性。
        
声明变量的修饰符：__strong, __weak, __unsafe_unretained, __autoreleasing;
        
声明属性的修饰符：strong, weak, unsafe_unretained。
        
对象和Core Foundation-style对象直接的转换修饰符号：__bridge，__bridge_retained或CFBridgingRetain, __bridge_transfer或CFBridgingRelease。
        
 对于线程的安全，有nonatomic，这样效率就更高了，但是不是线程的。如果要线程安全，可以使用atomic，这样在访问是就会有线程锁。
        
记住内存管理法则：谁使对象的引用计数+1，不再引用时，谁就负责将该对象的引用计数-1。

其中
 ARC：assign、weak、strong、copy
 MRC：assign、retain、copy、nonatomic、atomic
 </em>

##strong(ARC)
 - 直接赋值，对象引用计数+1
 - 功能等价于MRC里面的retain(在 ARC 里替代了 retain 的作用)
```objc
        // 默认会是什么呢？
        @property (nonatomic) NSString *name;
        // 默认是strong类型
        @property (nonatomic) NSArray *array;
```

##weak(ARC)


##assign(ARC/MRC)
  - assign在ARC和MRC中都是存在的
  - assign一般用来修饰基本数据类型,这个修饰词是直接赋值的意思,使用assign: 对基础数据类型 （NSInteger，CGFloat）和C数据类型（int, float, double, char, 等等）
  - assign也可用来修饰对象,但是，对象的引用计数不会+1（与strong的区别）
  - assign如果用来修饰对象属性，当对象销毁后指针不会指向nil，会出现野指针错误(与weak的区别)
  - 在MRC用assign来修饰代理，是为了防止循环引用。
  - 如果没有使用 weak strong retain copy 修饰 , 那么默认就是使用 assign 了. ( 它们之间是有你没我的关系 )
  

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  