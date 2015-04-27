### zbar 模拟器运行方法


［编译报错解决］duplicate symbol _base64_encode解决办法   

今天，闲来没事，想在模拟器上运行一下我的app。但是模拟器运行时，编译报错，

duplicate symbol _base64_encode in:

.../libzbar.a(symbol.o)

.../tencentOpenAPI(base64.o)



说的很清楚，就是这两个库中都定义了_base64_encode,所以编译器就会报错重复定义。
查了网上的一些方法，需要用到libtool什么的等等一些工具，或者删除_all_load等，都没什么用。
于是，只有一个办法了，去寻找源码，但是腾讯这种sdk，肯定是不会给源码的，只有去找libzbar的源码了。
先去下载zbar的源码     http://zbar.sourceforge.net/download.html

如图1，打开工程

如图2，找到symbol.c

如图3 找到base64_encode这个方法

找到base64_encode这个方法，更改这个方法名，比如，我改成了base64_en，同时，找到调用这个方法的那一行代码，修改为base64_en。然后再分别在真机和模拟器下运行工程，找到这2个libzbar.a文件。至于是否合并为通用的.a。就看你自己了。
合并的方法网上也很多了，非常简单。
打开终端，输入命令   lipo -create XXX(路径名)/Release-iphoneos/libzbar.a XXX(路径名)/Release-iphonesimulator/libzbar.a  -output  XXX(输出的路径名)/libzbar.a 
然后，导入这个libzbar.a，就可以在模拟器上运行项目了。
是不是很简单呢？呵呵。。。。
当然，我感觉这是比较偷懒或者简便的方法。如果没有源码的话，就要用到那些个什么麻烦的要死的工具了。
觉得不好，勿喷。和谐和谐。哈哈哈。。

如果如片加载不出来请参考原文
原文出自：http://www.cocoachina.com/bbs/read.php?tid=177828
