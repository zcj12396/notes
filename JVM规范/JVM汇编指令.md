## 常量 局部变量 和控制结构的使用

### for及if的控制结构

```java
public void spin(){       
    int i;        
    for(i=0;i<100;i++){
         ;
    }
}
```

汇编后：

```java
public void spin();//    
Code://       
	0: iconst_0      //把int型的0压入操作数栈  int i ;       
    1: istore_1      // 把int型的0从操作数栈弹出存储到局部变量表1的位子（0为this）//       
    2: iload_1       //加载局部变量表位置1的元素到操作数栈//       
    3: bipush        100  // 在操作数栈中创建100的元素//       
    5: if_icmpge     14   // 对比两个操作数//       
    8: iinc          1, 1   //位置1的元素自增1//       
    11: goto         2//       
    14: return//}
```

条件控制结构 if 对应的是if_icmpge

其在官方文档中的解释：

if_icmpge  pops the top two ints off the stack and compares them.

If value2 is greater than or equal to value1, execution branches to the address (pc + branchoffset),where pc is the address of the if_icmpge opcode in the bytecode and branchoffset is a 16-bit signed integer parameter following the if_icmpge opcode in the bytecode.

If value2 is less than value1, execution continues at the next instruction.

栈中value1 == 100 ，value2 == i （后进先出）



### 算数运算

通常需要继续操作数栈来进行计算（iinc除外，它是直接针对局部变量表中某一int进行自增）

### 访问运行时常量池

对于int、float、double、long以及标识String实例的引用，由ldc、ldc_w、ldc2_w指令来管理

### 方法调用

```java
int add(){
    return addTWO(1,2)
}
```

编译后的代码：

```java
Method int add():
aload_0   // 将常量表0数据（this)压入操作数栈
bipush 1   //
bipush 2   //1和2 压入操作数栈
invokevirtual  #4  //创建一个新的栈帧，并把this，1，2分别放入新栈帧局部变量表0，1，2的位置
ireturn //方法调用结束后，计算结果会直接放入操作数栈
```







#### tips

- Java虚拟机指令由一个字节来表示，所以最多不能超过256条，所以省略了一些例如byte,short,byte的操作指令，转由int类型的指令代替，这并不会影响到实际的结果。唯一的代价只是要把计算操作结果截断到它们的有效位置。因为byte、short、int及引用类型在局部变量表中都是占一个字的大小（在32位计算机中为4个字节）
- JVM对于long和double的支持与int相比，缺少了条件转移指令部分（由于long和double的存储在局部变量表中占两个字）