## JVM规范  数据类型

#### java虚拟机支持的数据类型

- 数值类型
  - byte : 8位有符号二进制补码整数，默认为0    -2<sup>7</sup>>~2<sup>7</sup>-1
  - short ： 16位同上     -2<sup>15</sup>>~2<sup>15</sup>-1
  - int ：32位同上           -2<sup>31</sup>>~2<sup>31</sup>-1
  - long ：64位同上         -2<sup>63</sup>>~2<sup>63</sup>-1
  - char ： 16位无符号整数，默认位为Unicode的null -->  '\u0000'             0-2<sup>16</sup>
  - float : 指单精度浮点数集合（或单精度扩展指数）中的一个元素。默认为正数0
  - double ： 指双精度浮点数集合（或双精度扩展指数）中的一个元素。默认为正数0
- boolean类型 ： true or false   Java语言所操作的boolean类型，在编译后都是用int来代替
- returnAddress类型：指向某个操作码（opcode）的指针，操作码与Java虚拟机指令相对应。



#### 引用类型

- 类类型   指向动态创建的类实例（ClassLoader加载）
- 数组类型  数组实例
- 接口类型  实现了某个接口的类实例和或数组实例