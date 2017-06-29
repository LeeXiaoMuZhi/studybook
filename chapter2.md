
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
##retain ( MRC )
 
 1.release 旧对象( 旧对象计数器 -1 ) , retain 新对象( 新对象计数器 +1 ) , 然后指向新对象 .
 2.在set方法里面是这样的 :
 
  ```objc
    if (_dog != nil)
    {
    [_dog release];
    }
    _dog = [dog retain];
  ```
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
  
##copy(ARC/MRC)
 
 - 在 MRC 时是这样做的 release 旧对象( 旧对象计数器 -1 ) , copy 新对象( 新对象计数器 +1 ) , 然后指向新对象 。
 
  - copy 在 MRC 时是这样做的 release 旧对象( 旧对象的引用计数器 -1 ) , copy 新对象( 新对象的引用计数器 +1 ) , 然后指向新对象 .（新对象是指最终指向的那个对象，不管深拷贝还是浅拷贝）
 
   - 在set方法里面是这样的 :
     ```objc
    if (_dog != nil)
    {
     [_dog release];
    }
    _dog = [dog copy];
   ```
 
 - 在 ARC 时是这么干的 copy 新对象( 新对象计数器 +1 ) , 然后指向新对象 。
  - copy 在 ARC 时是这么干的 copy 新对象( 新对象的引用计数器 +1 ) , 然后指向新对象 .
 
   - 在set方法里面是这样的 :
     ```objc
     _dog = [dog copy];
     ```
 - 使用注意 :
 
  -  修饰的属性本身要不可变的。例如 NSMutableArray 采用 copy 修饰 , 在addObject时会出现Crash， 因为NSMutableArray的对象在copy 过后就会变成NSArray。如果需要copy NSMutableArray对象，用：mutablecopy。
  -  遵守 NSCopying 协议的对象使用 .
 
 
 - block声明使用copy
    ```objc
     在使用block时，尽量使用typedef来起一个别名，这样更容易阅读。使block作为属性时，使用copy。
     typedef void (^HYBTestBlock)(NSString *name);
     @property (nonatomic, copy) HYBTestBlock testBlock;
     字符串 
     对于字符串，通常都是使用copy的方式。虽然使用strong似乎也没有没有问题，但是事实上在开发中都会使用      copy。为什么这么做？因为对于字符串，我们希望是一次内容的拷贝，外部修改也不会影响我们的原来的值，而且NSString类遵守了NSCopying, NSMutableCopying, NSSecureCoding协议。
    下面时使用copy的方式，验证如下：
    NSString *hahaString = @"哈哈";
    NSString *heheString = [hahaString copy];
     // 哈哈, 哈哈
    NSLog(@"%@, %@", hahaString, heheString);
    heheString = @"呵呵";
    // 哈哈, 呵呵
    NSLog(@"%@, %@", hahaString, heheString);
    我们修改了heheString，并不会影响到原来的hahaString。copy一个对象变成新的对象(新内存地址) 引用计数为1 原来对象计数不变。
```
##nonatomic/atomic( ARC/MRC) 
  
###非原子属性(nonatomic):
 - 不对set方法加锁（@synchronized）,互斥锁是利用线程同步实现的 , 意在保证同一时间只有一个线程调用 set 方法 。
 - 性能好
 - 线程不安全
 
###原子属性(atomic):
 - 原子属性就是对生成的 set 方法加互斥锁 @synchronized(锁对象)
 - 需要消耗系统资源
 - 还有 get 方法 , 要是同时 set 和 get 一起调用还是会有问题的 . 所以即使用了 atomic 修饰 还是不够安全 .  
  
  <em>
  1.原子属性就是对生成的 set 方法加互斥锁 @synchronized(锁对象) .
 
 @synchronized(self) { _delegate = delegate;}
 2.需要消耗系统资源 .
 3.互斥锁是利用线程同步实现的 , 意在保证同一时间只有一个线程调用 set 方法 .
 4.其实还有 get 方法 , 要是同时 set 和 get 一起调用还是会有问题的 . 所以即使用了 atomic 修饰 还是不够安全 .
 
 nonatomic 和 atomic 的介绍和区别
 
 1. 什么是atomicity（原子性）？
 
 atomicity（原子性）：我把原子性理解成线程对属性的单一执行。
 例如，当两条线程同时执行一个属性的set方法的时候，如果不具有原子性（也就是声明属性时使用了nonatimic），那么就可能出现当A线程正在改写某属性值的时候，B线程也许会突然闯入，把尚未修改好的属性值读取出来。发生这种情况时，线程读取到的属性值肯能不对。
 
 2. 保证atomicity真的就线程安全了吗？为什么日常声明都用的是nonatomic呢？
 
 1.保证atomicity并非也是线程安全的，如果需要保证安全，需要跟深层次的线程锁定机制。
 2.使用同步锁在iOS中开销比较大，会给程序带来性能上的问题。
 
 3. 为什么atomicity也不能保证线程安全？
 
 例如：当使用atomic时，仍然可能出现线程错误：当线程A进行set操作，这时其他线程的get或者set操作会因为等该操作而等待。当A线程的set操作结束后，B线程进行set操作，然后当A线程需要get操作时，却获得了在B线程的值，这就破坏了线程安全，如果有C线程在A线程get操作之前release了该属性，那么还会导致程序崩溃。所以仅仅使用atomic并不会使得线程安全，我们还是要为线程添加lock来确保线程的安全。
 
  </em>
  
##六、readOnly(只读)、readWrite(读写)
 
###readOnly：
- 只读属性，意味着只合成getter方法
- 不想把暴露的属性被别人替换，可以使用readOnly修饰
 
###readWrite：
- 生成getter和setter方法
-  不用readOnly修饰时候，默认为readWrite  
  
 ##关系、区别、总结
<em>

###属性声明修饰符
 
 属性声明修饰符有：strong, weak, unsafe_unretained, readWrite，默认strong, readWrite的。
 
 strong：strong和retain相似,只要有一个strong指针指向对象，该对象就不会被销毁
 
 weak：声明为weak的指针，weak指针指向的对象一旦被释放，weak的指针都将被赋值为nil；
 
 unsafe_unretained：用unsafe_unretained声明的指针，指针指向的对象一旦被释放，这些指针将成为野指针。
 
 @property (nonatomic, copy) NSString *name;
 // 一旦所指向的对象被释放，就会成为野指针
 @property (nonatomic, unsafe_unretained) NSString *unsafeName;
 
 lili.name = @"Lili";
 lili.unsafeName = lili.name;
 lili.name = nil;
 // unsafeName就变成了野指针。这里不会崩溃，因为为nil.
 NSLog(@"%@", lili.unsafeName);
 ###weak 和 assign的区别
 
 assign与weak，它们都是弱引用声明类型，但是他们是有区别的。
 1.用weak声明的变量在栈中就会自动清空，赋值为nil。
 2.用assign声明的变量在栈中可能不会自动赋值为nil，就会造成野指针错误！
 
 以delegate的声明为例，在MRC中多delegate声明使用的是assign，这是为了不造成循环引用，这时，我们需要在-dealloc方法中写上 self.delegate = nil，以免造成delegate的野指针错误。当然，在ARC中，只需要用weak声明delegate，就会自动释放了。
###strong/weak


- strong：强引用，也是我们通常说的引用，其存亡直接决定了所指向对象的存亡。如果不存在指向一个对象的引用，并且此对象不再显示在列表中，则此对象会被从内存中释放。
 weak：弱引用，不决定对象的存亡。即使一个对象被持有无数个弱引用，只要没有强引用指向它，那么还是会被清除。
 strong与retain功能相似；weak与assign相似，只是当对象消失后weak会自动把指针变为nil;

- 如果属性没有指定类型，默认是什么呢？其实是strong。如果证明呢？验证方法：分别将array属性的类型分别设置为weak, assign,strong,不设置，这四种情况的结果分别是：第一种打印为空，第二种直接直接崩溃，第三种和最后一种是可以正常使用。如下面的验证代码：
 
 Person *lili = [[Person alloc] init];
 lili.name = @"LiLi";
 lili.copiedString = @"LiLi\' father is LLL";
 lili.array = @[@"谢谢", @"感谢"];
 
 NSArray *otherArray = lili.array;
 lili = nil;
 NSLog(@"%@", otherArray);
 
 再继续添加下面的代码。默认声明变量的类型为__strong类型，因此上面的NSArray *otherArray = lili.array;与__strong NSArray *otherArray = lili.array;是一样的。如果我们要使用弱引用，特别是在解决循环强引用时就特别重要了。我们可以使用__weak声明变量为弱引用，这样就不会增加引用计数值。
 
 
 __strong NSArray *strongArray = otherArray;
 otherArray = nil;
 // 打印出来正常的结果。
 NSLog(@"strongArray = %@", strongArray);
 
 
 __weak NSArray * weakArray = strongArray;
 strongArray = nil;
 // 打印出来：null
 NSLog(@"weakArray: %@", weakArray);


### 深拷贝与浅拷贝
 
 关于浅拷贝，简单来说，就像是人与人的影子一样。而深拷贝就像是梦幻西游中的龙宫有很多个长得一样的龙宫，但是他们都是不同的精灵，因此他们各自都是独立的。
 
 我相信还有不少朋友有这样一种误解：浅拷贝就是用copy，深拷贝就是用mutableCopy。如果有这样的误解，一定要更正过来。copy只是不可变拷贝，而mutableCopy是可变拷贝。比如，NSArray *arr = [modelsArray copy]，那么arr是不可变的。而NSMutableArray *ma = [modelsArray mutableCopy]，那么ma是可变的。
 
 lili.array = [@[@"谢谢", @"感谢"] mutableCopy];
 NSMutableArray *otherArray = [lili.array copy];
 lili.array[0] = @"修改了谢谢";
 // 打印： 谢谢 修改了谢谢
 // 说明数组里面是字符串时，直接使用copy是相当于深拷贝的。
 NSLog(@"%@ %@", otherArray[0], lili.array[0]);
 这里就是浅拷贝，但是由于数组中的元素都是字符串，因此不会影响原来的值。
 
 数组中是对象时：
 
 NSMutableArray *personArray = [[NSMutableArray alloc] init];
 Person *person1 = [[Person alloc] init];
 person1.name = @"lili";
 [personArray addObject:person1];
 
 Person *person2 = [[Person alloc] init];
 person2.name = @"lisa";
 [personArray addObject:person2];
 
 // 浅拷贝
 NSArray *newArray = [personArray copy];
 Person *p = newArray[0];
 p.name = @"lili的名字被修改了";
 
 // 打印结果：lili的名字被修改了
 // 说明这边修改了，原来的数组对象的值也被修改了。虽然newArray和personArray不是同一个数组，不是同一块内存，
 // 但是实际上两个数组的元素都是指向同一块内存。
 NSLog(@"%@", ((Person *)(personArray[0])).name);
 
 深拷贝，其实就是对数组中的所有对象都创建一个新的对象：
 
 NSMutableArray *personArray = [[NSMutableArray alloc] init];
 Person *person1 = [[Person alloc] init];
 person1.name = @"lili";
 [personArray addObject:person1];
 
 Person *person2 = [[Person alloc] init];
 person2.name = @"lisa";
 [personArray addObject:person2];
 
 // 深拷贝
 NSMutableArray *newArray = [[NSMutableArray alloc] init];
 for (Person *p in personArray) {
 Person *newPerson = [[Person alloc] init];
 newPerson.name = p.name;
 
 [newArray addObject:newPerson];
 }
 Person *p = newArray[0];
 p.name = @"lili的名字被修改了";
 
 // 打印结果：lili
 NSLog(@"%@", ((Person *)(personArray[0]))
 
 
 ###assign、copy、retain
 assign：默认类型，setter方法直接赋值，不进行任何retain操作，不改变引用计数。一般用来处理基本数据类型。
 retain：释放旧的对象（release），将旧对象的值赋给新对象，再令新对象引用计数为1。我理解为指针的拷贝，拷贝一份原来的指针，释放原来指针指向的对象的内容，再令指针指向新的对象内容。
 copy：与retain处理流程一样，先对旧值release，再copy出新的对象，retainCount为1.为了减少对上下文的依赖而引入的机 制。我理解为内容的拷贝，向内存申请一块空间，把原来的对象内容赋给它，令其引用计数为1。对copy属性要特别注意：被定义有copy属性的对象必须要 符合NSCopying协议，必须实现- (id)copyWithZone:(NSZone *)zone方法。
 也可以直接使用：
 使用assign: 对基础数据类型 （NSInteger，CGFloat）和C数据类型（int, float, double, char, 等等）
 使用copy： 对NSString
 使用retain： 对其他NSObject和其子类
 
 #### retain, copy, assign区别
 1. 假设你用malloc分配了一块内存，并且把它的地址赋值给了指针a，后来你希望指针b也共享这块内存，于是你又把a赋值给（assign）了b。此时a 和b指向同一块内存，请问当a不再需要这块内存，能否直接释放它？答案是否定的，因为a并不知道b是否还在使用这块内存，如果a释放了，那么b在使用这块 内存的时候会引起程序crash掉。
 
 2. 了解到1中assign的问题，那么如何解决？最简单的一个方法就是使用引用计数（reference counting），还是上面的那个例子，我们给那块内存设一个引用计数，当内存被分配并且赋值给a时，引用计数是1。当把a赋值给b时引用计数增加到 2。这时如果a不再使用这块内存，它只需要把引用计数减1，表明自己不再拥有这块内存。b不再使用这块内存时也把引用计数减1。当引用计数变为0的时候， 代表该内存不再被任何指针所引用，系统可以把它直接释放掉。
 
 3. 上面两点其实就是assign和retain的区别，assign就是直接赋值，从而可能引起1中的问题，当数据为int, float等原生类型时，可以使用assign。retain就如2中所述，使用了引用计数，retain引起引用计数加1, release引起引用计数减1，当引用计数为0时，dealloc函数被调用，内存被回收。
 
 4. copy是在你不希望a和b共享一块内存时会使用到。a和b各自有自己的内存。
 
 5. atomic和nonatomic用来决定编译器生成的getter和setter是否为原子操作。在多线程环境下，原子操作是必要的，否则有可能引起错误的结果。加了atomic，setter函数会变成下面这样：
 if (property != newValue) {
 [property release];
 property = [newValue retain];
 }
 ####深入理解一下(包括autorelease)
1. retain之后count加一。alloc之后count就是1，release就会调用dealloc销毁这个对象。
如果 retain，需要release两次。通常在method中把参数赋给成员变量时需要retain。
例如：
ClassA有 setName这个方法：
-(void)setName:(ClassName *) inputName
{
name = inputName;
[name retain]; //此处retian，等同于[inputName retain],count等于2
}
调用时：
ClassName *myName = [[ClassName alloc] init];
[classA setName:myName]; //retain count == 2
[myName release]; //retain count==1，在ClassA的dealloc中release name才能真正释放内存。
2. autorelease 更加tricky，而且很容易被它的名字迷惑。我在这里要强调一下：autorelease不是garbage collection，完全不同于Java或者.Net中的GC。
autorelease和作用域没有任何关系！
autorelease 原理：
a.先建立一个autorelease pool
b.对象从这个autorelease pool里面生成。
c.对象生成 之后调用autorelease函数，这个函数的作用仅仅是在autorelease pool中做个标记，让pool记得将来release一下这个对象。
d.程序结束时，pool本身也需要rerlease, 此时pool会把每一个标记为autorelease的对象release一次。如果某个对象此时retain count大于1，这个对象还是没有被销毁。
上面这个例子应该这样写：
ClassName *myName = [[[ClassName alloc] init] autorelease];//标记为autorelease
[classA setName:myName]; //retain count == 2
[myName release]; //retain count==1，注意，在ClassA的dealloc中不能release name，否则release pool时会release这个retain count为0的对象，这是不对的。
记住一点：如果这个对象是你alloc或者new出来的，你就需要调用release。如果使用autorelease，那么仅在发生过retain的时候release一次（让retain count始终为1）。
3 xcode 中的新标记 strong weak
strong 用来修饰强引用的属性；对应以前retain
weak 用来修饰弱引用的属性；对应以前的assign
 
 ###Getter/Setter
 - getter：是用来指定get方法的方法名
   setter：是用来指定set访求的方法名
   在@property的属性中，如果这个属性是一个BOOL值，通常我们可以用getter来定义一个自己喜欢的名字，例如：
   @property (nonatomic, assign, getter=isValue) boolean value;
   @property (nonatomic, assign, setter=setIsValue) boolean value;
  - 在ARC下，getter/setter的写法与MRC的不同了。我面试过一些朋友，笔试这关就写得很糟（不包括算法）。通常在笔试时都会让重写一个属性的Getter/Setter方法。
 
 @property (nonatomic, strong) NSMutableArray *array;
 
 -(void)setArray:(NSMutableArray *)array {
 if (_array != array) {
 _array = nil;
 
 _array = array;
 }
 }
 如果是要重写getter就去呢？就得增加一个变量了，如果同时重写getter/setter方法，就不会自动生成_array变量，因此我们可以声明一个变量为_array:
 
 - (void)setArray:(NSMutableArray *)array {
 if (_array != array) {
 _array = nil;
 
 _array = array;
 }
 }
 
 - (NSMutableArray *)array {
 return _array;
 }
 

 
 
 ###总结
 所有的属性，都尽可能使用nonatomic，以提高效率，除非真的有必要考虑线程安全。
 
 NSString：通常都使用copy，以得到新的内存分配，而不只是原来的引用。
 
 strong：对于继承于NSObject类型的对象，若要声明为强使用，使用strong，若要使用弱引用，使用__weak来引用，用于解决循环强引用的问题。
 
 weak：对于xib上的控件引用，可以使用weak，也可以使用strong。
 
 __weak：对于变量的声明，如果要使用弱引用，可以使用weak，如：weak typeof(Model) weakModel = model;就可以直接使用weakModel了。
 
 __strong：对于变量的声明，如果要使用强引用，可以使用strong，默认就是strong，因此不写与写__strong声明都是一样的。
 
 unsafe_unretained：这个是比较少用的，几乎没有使用到。在所引用的对象被释放后，该指针就成了野指针，不好控制。
 
 __unsafe_unretained：也是很少使用。同上。
 
 __autoreleasing：如果要在循环过程中就释放，可以手动使用__autoreleasing来声明将之放到自动释放池。
</em>  
  
  
  
  
  
  
  
  