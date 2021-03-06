#java方法分派
>1.描述动态扩展能力
>>1.1方法调用并不等同于方法执行，方法调用的时间在方法运行之前.
>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;方法调用阶段唯一的任务就是确定调用方法的版本(即绑定调用哪一个方法)，暂时还不涉及方法内部的具体运行过程<br>
>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在程序运行时，进行方法调用是最普遍、最频繁的操作。在Class文件的编译过程中不包含传统编译中的连接步骤，一切方法调用在Class文件里存储的都只是符号引用，而不是方法在实际运行时内存布局中的入口地址(相当于直接引用 明确的函数入口地址)
>
>2.方法解析
>>2.1所有方法调用中的目标方法在Class文件里面都是一个常量池中的符号引用，在类加载的解析阶段，会将其中一部分符号引用转化为直接引用，不会延迟到运行期再去完成.
>>>解析能成立的前提是：方法在程序真正运行之前就有一可确定的调用版本，并且这个方法的调用版本是运行期是不可改变的<调用目标在程序代码写好、编译器进行编译时就必须确定下来>
>
>>2.2满足方法解析性质(编译期可知，运行期不可变)的方法
>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;静态方法和私有方法.前者与类型直接相关联，后者在外部不可被访问，这两种方法都不可能通过继承或者别的方式重写出其它版本，因此它们都适合在类加载阶段进行静态解析<br>
>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Java虚拟机里提供了四条方法调用字节码指令<br>
>>>>a.invokestatic:调用静态方法<br>
>>>>b.invokespecial:调用实例构造器<init>方法，私有方法和父类方法()<br>
>>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;只要能被invokestatic与invokespecial指令调用的方法，都可以在解析阶段确定唯一的调用版本 既:<br>
>>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;静态方法，私有方法，实例构造器和父类方法(类加载的时候就会把符号引用解析为该方法的直接引用:非虚方法)<br>
>>>>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;非虚方法除了使用invokestatic与invokespecial,还有一种，就是被final修饰的方法。虽然final方法是使用invokevirtual指令来调用的，但是由于它无法被覆盖，没有其它版本，所以也无须对方法接收都进行多态选择，又或者说多态选择的结果是唯一的(JAVA规范明确是一种非虚方法)<br>
>>>>c.invokevirtual:调用虚方法(运行时绑定)注意：static修饰的方法与final修饰且非private的方法为虚方法 但是用的invokestatic或invokespecial调用<br>
>>>>d.invokeinterface:调用接口方法，会在运行时再确定一个实现此接口的对象<br>
>
>3.分派
>>3.1分派的时间分为:静态和动态。宗量数:单分派与多分派。分派时间和宗量数目结合分为:静态单分派，静态多分派，动态单分派与动态多分派情况<br>
>>3.2静态分派<方法重载 根据参数的数量和数据类型的静态类型>
>![](http://i.imgur.com/dxmPtW2.jpg)
>>3.3动态分派<指向实际类型>
>
>>![](http://i.imgur.com/qP9AL3m.jpg)
>>>3.3.1 invokevirtual指令的运行时解析过程大致分为以下步骤
>>>>3.3.1.1.如果在类型C中找到与常量中描述符和简单名称都相同的方法，则进行访问权限校验，如果通过则返回这个方法的直接引用，查找结束；不通过则返回java.lang.IllegalAccessError错误<br>
>>>>3.3.1.2 否则，按照继承关系从下往上依次对C的各个父类进行第2步的搜索与校验过程<br>
>>>>3.3.1.3.如果始终没有找到合适的方法，则抛出java.lang.AbstractMethodError错误<br>
>>>注意:invokevirtual指令执行的第一步就是在运行期确定接收者的实际类型，所以两次调用中的invokevirtual指令把常量池中的类方法符号引用解析到了不同的直接引用上，这个过程就是Java语言中方法重写的本质。我们把这种在运行期根据实际类型确定方法执行版本的分派过程称为动态分派。
>
>>3.4 静态和动态两者的比较
> ![](http://i.imgur.com/4I86Nxn.png)

>>3.5 单分派与多分派
>>>3.5.1宗量:方法的接受者与方法的参数统称。根据分派基于多少种宗量，可以将分派划分为单分派(方法的接受者)和多分派(方法重载个数)<br>
>>>3.5.2![](http://i.imgur.com/rwdXMt7.png)
