---
title: Runtime术语
date: 2016-06-10 21:14:35
tags: Runtime
---

### id和Class
<!--more-->

objc/objc.h 文件

	#if !OBJC_TYPES_DEFINED
	/// An opaque type that represents an Objective-C class.
	typedef struct objc_class *Class;

	/// Represents an instance of a class.
	struct objc_object {
	    Class isa  OBJC_ISA_AVAILABILITY;
	};

	/// A pointer to an instance of a class.
	typedef struct objc_object *id;
	#endif

Class是一个指向objc_class结构体的指针,Class就是我们所说的类

id是一个指向objc_object结构体的指针,id就是我们所说的对象

runtime.h 文件

	typedef struct objc_class *Class;
	struct objc_class { 
	 Class isa                                 OBJC_ISA_AVAILABILITY; // metaclass
	#if !__OBJC2__
	 Class super_class                         OBJC2_UNAVAILABLE; // 父类
	 const char *name                          OBJC2_UNAVAILABLE; // 类名
	 long version                              OBJC2_UNAVAILABLE; // 类的版本信息，默认为0，可以通过runtime函数class_setVersion或者class_getVersion进行修改、读取
	 long info                                 OBJC2_UNAVAILABLE; // 类信息，供运行时期使用的一些位标识，如CLS_CLASS (0x1L) 表示该类为普通 class，其中包含实例方法和变量;CLS_META (0x2L) 表示该类为 metaclass，其中包含类方法;
	 long instance_size                        OBJC2_UNAVAILABLE; // 该类的实例变量大小（包括从父类继承下来的实例变量）
	 struct objc_ivar_list *ivars              OBJC2_UNAVAILABLE; // 该类的成员变量地址列表
	 struct objc_method_list **methodLists     OBJC2_UNAVAILABLE; // 方法地址列表，与 info 的一些标志位有关，如CLS_CLASS (0x1L)，则存储实例方法，如CLS_META (0x2L)，则存储类方法;
	 struct objc_cache *cache                  OBJC2_UNAVAILABLE; // 缓存最近使用的方法地址，用于提升效率；
	 struct objc_protocol_list *protocols      OBJC2_UNAVAILABLE; // 存储该类声明遵守的协议的列表
	#endif
	}
	/* Use `Class` instead of `struct objc_class *` */

isa：objc_object（实例对象）中isa指针指向的类结构称为class（也就是该对象所属的类）其中存放着普通成员变量与动态方法（“-”开头的方法）；此处isa指针指向的类结构称为metaclass，其中存放着static类型的成员变量与static类型的方法（“+”开头的方法）。

super_class： 指向该类的父类的指针，如果该类是根类（如NSObject或NSProxy），那么super_class就为nil。

类与对象的继承层次关系如图
![](http://7xt1bu.com1.z0.glb.clouddn.com/15.png)

所有的metaclass中isa指针都是指向根metaclass，而根metaclass则指向自身。根metaclass是通过继承根类产生的，与根class结构体成员一致，不同的是根metaclass的isa指针指向自身。

### SEL

SEL是selector在Objective-C中的表示类型。selector可以理解为区别方法的ID。

	typedef struct objc_selector *SEL;

objc_selector的定义如下：

	struct objc_selector {
	    char *name;                       OBJC2_UNAVAILABLE;// 名称
	    char *types;                      OBJC2_UNAVAILABLE;// 类型
    };

### IMP

在objc.h中得定义如下：

	typedef id (*IMP)(id, SEL, ...);

IMP是“implementation”的缩写，它是由编译器生成的一个函数指针。当你发起一个消息后（下文介绍），这个函数指针决定了最终执行哪段代码。

### Method

Method代表类中的某个方法的类型。
	typedef struct objc_method *Method;
objc_method的定义如下：

	struct objc_method {
	    SEL method_name                   OBJC2_UNAVAILABLE; // 方法名
	    char *method_types                OBJC2_UNAVAILABLE; // 方法类型
	    IMP method_imp                    OBJC2_UNAVAILABLE; // 方法实现
	}

方法名method_name类型为SEL，上文提到过。
方法类型method_types是一个char指针，存储着方法的参数类型和返回值类型。
方法实现method_imp的类型为IMP，上文提到过。

### Ivar

Ivar代表类中实例变量的类型  不太懂
	
	typedef struct objc_ivar *Ivar;

objc_ivar的定义如下：

	struct objc_ivar {
	    char *ivar_name                   OBJC2_UNAVAILABLE; // 变量名
	    char *ivar_type                   OBJC2_UNAVAILABLE; // 变量类型
	    int ivar_offset                   OBJC2_UNAVAILABLE; // 基地址偏移字节
	#ifdef __LP64__
	    int space                         OBJC2_UNAVAILABLE; // 占用空间
	#endif
	}

### objc_property_t

objc_property_t是属性，它的定义如下：

	typedef struct objc_property *objc_property_t;

objc_property是内置的类型，与之关联的还有一个objc_property_attribute_t，它是属性的attribute，也就是其实是对属性的详细描述，包括属性名称、属性编码类型、原子类型/非原子类型等。它的定义如下：

	typedef struct {
	    const char *name; // 名称
	    const char *value;  // 值（通常是空的）
	} objc_property_attribute_t;

### Cache

Catch的定义如下：

	typedef struct objc_cache *Cache
objc_cache的定义如下：

	struct objc_cache {
	    unsigned int mask                   OBJC2_UNAVAILABLE;
	    unsigned int occupied               OBJC2_UNAVAILABLE;
	    Method buckets[1]                   OBJC2_UNAVAILABLE;
	};

mask: 指定分配cache buckets的总数。在方法查找中，Runtime使用这个字段确定数组的索引位置。
occupied: 实际占用cache buckets的总数。
buckets: 指定Method数据结构指针的数组。这个数组可能包含不超过mask+1个元素。需要注意的是，指针可能是NULL，表示这个缓存bucket没有被占用，另外被占用的bucket可能是不连续的。这个数组可能会随着时间而增长。
objc_msgSend 每调用一次方法后，就会把该方法缓存到cache列表中，下次的时候，就直接优先从cache列表中寻找，如果cache没有，才从methodLists中查找方法。

### Category

这个就是我们平时所说的类别了，很熟悉吧。它可以动态的为已存在的类添加新的方法。
它的定义如下：

	typedef struct objc_category *Category;
objc_category的定义如下：

	struct objc_category {
		char *category_name                           OBJC2_UNAVAILABLE; // 类别名称
		char *class_name                              OBJC2_UNAVAILABLE; // 类名
		struct objc_method_list *instance_methods     OBJC2_UNAVAILABLE; // 实例方法列表
		struct objc_method_list *class_methods        OBJC2_UNAVAILABLE; // 类方法列表
		struct objc_protocol_list *protocols          OBJC2_UNAVAILABLE; // 协议列表
	}
