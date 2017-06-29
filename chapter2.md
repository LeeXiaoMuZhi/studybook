
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

@property与@synthesize是成对出现的，可以自动生成某个类成员变量的存取方法。在Xcode4.5以及以后的版本，@synthesize可以省略。

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
 - 弱指针是针对对象的修饰词 , 就是说它不能修饰基本数据类型 。
 - weak修饰的对象，引用计数不会+1，即直接赋值
 - 弱引用是为打破循环引用而生的，比如在Block中，block在copy的时候，会对内部使用到的对象的引用技术+1，如果使用[self 方法名]，那么就会有一个强指针指向self所在的class的内存地址，class的引用计数会+1，这样一来会导致class所在的内存地址无法被释放，造成内存泄漏 .
 - 它所指向的对象如果被销毁 , 它会指向 nil . 而 nil 访问什么鬼都不会报野指针错误 .(它最被人所喜欢的原因是 它所指向的对象如果被销毁 , 它会指向 nil . 从而不会出现野指针错误 .)

- xib/storybard连接的对象为什么可以使用weak:
    ```objc
    @property (nonatomic, weak) IBOutlet UIButton *button;
    像上面这行代码一样，在连接时自动生成为weak。因为这个button已经放到view上了，因此只要这个View不被释放，这个button的引用计数都不会为0，因此这里可以使用weak引用。
    如果我们不使用xib/storyboard，而是使用纯代码创建呢？
    @property (nonatomic, weak) UIButton *button;
    使用weak时，由于button在创建时，没有任何强引用，因此就有可能提前释放。Xcode编译器会告诉我们，这里不能使用weak。因此我们需要记住，只要我们在创建以后需要使用它，我们必须保证至少有一个强引用，否则引用计数为0，就会被释放掉。对于上面的代码，就是由于在创建时使用了weak引用，因此button的引用计数仍然为0，也就是会被释放，编译器在编译时会检测出来的。
    这样写，在创建时通过self.button = ...就是出现错误，因为这是弱引用。所以我们需要声明为强引用，也就是这样：
    @property (nonatomic, strong) UIButton *button;
    ```
##assign(ARC/MRC)
  - assign在ARC和MRC中都是存在的
  - assign一般用来修饰基本数据类型,这个修饰词是直接赋值的意思,使用assign: 对基础数据类型 （NSInteger，CGFloat）和C数据类型（int, float, double, char, 等等）
  - assign也可用来修饰对象,但是，对象的引用计数不会+1（与strong的区别）
  - assign如果用来修饰对象属性，当对象销毁后指针不会指向nil，会出现野指针错误(与weak的区别)
  - 在MRC用assign来修饰代理，是为了防止循环引用。
  - 如果没有使用 weak strong retain copy 修饰 , 那么默认就是使用 assign 了. ( 它们之间是有你没我的关系 )
  

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  