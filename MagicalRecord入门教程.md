### MagicalRecord入门教程

Magical Record是什么
在Cocoa中存在一种技术叫Core Data，用来对数据进行持久化，类似于Java世界中的Hibernate。在新建Cocoa Application/iOS Application的向导中，有一个选项是要不要使用Core Data，当启用以后你会发现在AppDelegate.m中添加了大量与Core Data相关的代码，但是你对大部分代码不知所以然。

Magical Record的出现在一定程度上缓解了这个问题，降低了Core Data的使用门槛。

Magical Record借用了Ruby on Rails中的Active Record模式，使得你可以非常容易的添加、查找、删除数据。Google了一下，没有发现中文相关教程，遂把自己的试用过程记录下来，写成此篇文章。

安装
新建一个项目，注意在向导中不要勾选Core Data。
下载Magical Record，并把MagicalRecord目录拖拽到工程中，记得勾选copy items into group folder。
为项目添加CoreData FrameWork。(点击工程根节点，然后依次Targets > Build Phases > Link Binary With Libraries > + > CoreData.framework > Add)。
添加Magical Record的头文件到*-Prefix.pch：
 #import "CoreData+MagicalRecord.h"
创建模型文件
下面创建一个名为Person的模型，有age、firstname、lastname三个字段。

创建一个名为Model的模型文件。 (File > New File… > Core Data > Data Model)
点击左下角的Add Entity，更改Entity的名字为Person。
为Entity添加三个Attribute：age(Integer16)、firstname(string)、lastname(string)。
点击Editor > Create NSManagedObject Subclass… > Create创建模型文件对应的类。
使用Magical Record
初始化Magical Record
首先在AppDelegate.m中添加以下代码对Magical Record进行初始化：

- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    [MagicalRecord setupCoreDataStackWithStoreNamed:@"MyDatabase.sqlite"];
    // ...
    return YES;
}
- (void)applicationWillTerminate:(NSNotification *)aNotification
{
    [MagicalRecord cleanUp];
}
是否比Core Data默认的初始化简洁多了呢？

查询记录
使用Person的MR_findAll、MR_findAllSortedBy、MR_findByAttribute等方法可以查询Person：

//查找数据库中的所有Person。
NSArray *persons = [Person MR_findAll];
//查找所有的Person并按照first name排序。
NSArray *personsSorted = [Person MR_findAllSortedBy:@"firstname" ascending:YES];
//查找所有age属性为25的Person记录。
NSArray *personsAgeEuqals25   = [Person MR_findByAttribute:@"age" withValue:[NSNumber numberWithInt:25]];
//查找数据库中的第一条记录
Person *person = [Person MR_findFirst];
添加记录
使用Person的MR_createEntity方法可以方便的创建一个Person，需要使用[[NSManagedObjectContext MR_defaultContext] MR_save]来进行保存哦：

Person *person = [Person MR_createEntity];
person.firstname = @"Frank";
person.lastname = @"Zhang";
person.age = @26;//此处使用了LLVM的新特性，XCode 4.4可用
[[NSManagedObjectContext MR_defaultContext] MR_save];
更新记录
直接对数据库中查找到的Person进行赋值，然后使用NSManagedObjectContext保存即可更新Person：

Person *person = ...;//此处略
person.lastname = object;        
[[NSManagedObjectContext MR_defaultContext] MR_save];
删除记录
使用Person的MR_deleteEntity可以方便的删除Person，模式和添加更新一致：

Person *person = ...;//此处略
[person MR_deleteEntity];
[[NSManagedObjectContext MR_defaultContext] MR_save];
小技巧
启动时MR_mergedObjectModelFromMainBundle方法报错
Core Data的模型有版本的概念，有可能在你Magical Record第一次初始化完成以后，你又更改了模型文件，导致Core Data去合并模型报错。解决办法很简单，点击菜单中的Project->Clean即可。

项目使用ARC后，编译Magical Record不通过
点击项目 -> Build Phases -> Compile Sources中, 双击报错的class文件, 编辑Compiler Flags加入 -fno-objc-arc。

不想使用MR_前缀
只需要在*-Prefix.pch文件中添加一句#define MR_SHORTHAND即可，注意这句要在#import “CoreData+MagicalRecord.h”之前。

参考链接：
Magical Record: how to make programming with Core Data pleasant
http://blog.csdn.net/kuizhang1/article/details/21200367
