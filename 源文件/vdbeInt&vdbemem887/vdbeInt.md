# vdbeInt.h简介
它是vdbe.c的私有头文件，定义了VDBE的常用数据结构。
主要包含：一些结构体和方法的声明或者预定义

## 一些方法和结构体的条件编译和预处理
```
typedef struct VdbeOp Op;

/*
** Boolean values
*/
typedef unsigned char Bool;

/* Opaque type used by code in vdbesort.c
**Opaque类型被vdbesort.c文件中的代码使用
*/
typedef struct VdbeSorter VdbeSorter;```

```

/* Opaque type used by the explainer 这个类型被解释器使用*/
typedef struct Explain Explain;
#define MEM_Null      0x0001   /* Value is NULL */
#define MEM_Str       0x0002   /* Value is a string */
#define MEM_Int       0x0004   /* Value is an integer */
#define MEM_Real      0x0008   /* Value is a real number 值为一个实数*/
#define MEM_Blob      0x0010   /* Value is a BLOB 只为一个块儿*/
#define MEM_RowSet    0x0020   /* Value is a RowSet object 值为一个行设置对象*/
#define MEM_Frame     0x0040   /* Value is a VdbeFrame object 值为一个Vdbe框架对象*/
#define MEM_Invalid   0x0080   /* Value is undefined */
#define MEM_TypeMask  0x00ff   /* Mask of type bits 隐藏类型的二进制数*/```

## 几个重要结构体的分析
```
struct VdbeCursor(vdbe游标)
struct VdbeFrame（vdbe框架）
struct Mem（仅对vdbe可见的数据结构）
struct VdbeFunc（辅助数据）
struct sqlite3_context
（上下文和返回值等信息）
struct Explain（vdbe的信息）
struct Vdbe（vdbe整体结构）
struct VdbeCursor
｛
   …
   int iDb (检索数据库)
   Bool isTable; （查看表是否存在）
   Bool rowidIsValid（某一行的数据是否有效）
   i64 lastRowid
   int seekResult（查询结构）
   …
｝（树形结构，数据库页为子节点）
游标是一个数据库文件中单一BTree的指示器。
游标能够找到拥有特定值的一个BTree的入口，或者遍历BTree的所有入口。你能够从新的BTree入口或者从这个指针通常指向的的入口检索值或者数据。每一个虚拟机已经开启的指针都代表着一个一下结构的常量。
struct VdbeFrame {
 Vdbe *v;
 Op *aOp;
 Mem *aMem (父框架信息)
 …
 Vdbe *v;
 VdbeFrame *pParent;
 Op *aOp;
 Mem *aMem;（子框架信息）
}
```
一个虚拟机框架对象的内存由父框架的一个存储单元中被分配和管理。当内存单元被删除或者重写，虚拟机框架对象不会被立即释放。相反的，它将被链接到Vdbe.pDelFrame清单中。当虚拟机在中重启后，Vdbe.pDelFrame的目录清单被删除。这么做而不是立刻删除虚拟机框架对象是为了避免当属于子框架的存储单元被释放时循环调用sqlite3VdbeMemRelease()方法。


```
struct Vdbe｛
  sqlite3 *db;
  Op *aOp；
  Mem *aMem; （内存地址）
  Mem **apArg（参数）
  Mem *aColName;
  Mem *pResultSet;
  …｝```



  这个结构体定义了一个虚拟机的实体。这个结构体包含了虚拟机完整的状态。
sqlite3_stmt这个结构的指针由sqlite3_prepare()方法返回，它是这个结构实体的一个真实指针。Vdbe.inVtab方法的变量在vdbe程序产生的任何虚拟表方法调用期间被置为零。
这个变量主要用于两个目的：允许xDestroy方法实现删除表的声明以及为了防止分配内存失败产生的副作用。sqlite3_stmt这个结构的指针由sqlite3_prepare()方法返回，它是这个虚拟机实体的一个真实指针








