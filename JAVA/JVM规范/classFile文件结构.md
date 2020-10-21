# ClassFile文件结构

u1:一个字节的无符号数

在class文件中，各项按照严格顺序存放，没有用任何填充或者对齐作为各项间的分隔符号

```c
ClassFile{
	u4  magic; // 魔数，固定为"0xCAFEBABY"
    u2  minor_version; //jdk次版本号
    u2  major_version;  //jdk主版本号
    u2  constant_pool_count;  //常量池数组大小，从1计数
    cp_info  constant_pool[constant_pool_count - 1]; //常量池数组
    u2  access_flags;  //类的访问标志，如：public
    u2  this_class;  //类索引，指向常量池中的类符号引用
    u2  super_class;  //父类索引，指向常量池中的类符号引用
    u2  interfaces_count; //实现的接口的数量
    u2  interfaces[interfaces_count]; //接口列表，按implements后面的接口顺序
    u2  fields_count;  //字段数
    field_info  fields[fields_count]; //字段表
    u2  methods_count; //方法数
    method_info  methods[methods_count]; //方法表
    u2  attributes_count; //属性表大小
    attribute_info  attributes[attributes_count]; //属性表
}
```



#### 常量池

是一种表结构，包含class文件结构及其子结构中引用的所有字符串常量、类或接口名、字段名和其他常量。

常量池中的每一项都要具备相同的特征：第一个字节为类型标记，用于确定这个常量的类型。

tag标记类型标记出这个常量所属类型：CONSTANT_XXXX_info

method表常量中只包含当前类的方法，不包含父类。

##### 属性和字段

