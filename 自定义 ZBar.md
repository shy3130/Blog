## 自定义 ZBar
最近在做的项目中需要用到二维码扫描功能，之前在Android中使用过ZXing识别二维码，ZXing也有对应的iOS版本，经过了解，ZBar也是一个常用的二维码识别软件，并分别提供了iOS和Android的SDK可供使用，最终我选择了ZBar进行二维码识别，它的注释清晰，便于使用。

ZBar为我们提供了两种使用方式，一种是直接调用ZBar提供的ZBarReaderViewController打开一个扫描界面，另一种方式是使用ZBar提供的可以嵌在其他视图中的ZBarReaderView，实际项目中我们更可能会使用第二种方式，这可以让我们对界面做更多的定制。

ZBar使用起来也非常简单，将ZBarSDK导入项目，在需要使用ZBar的文件中导入ZBarSDK.h头文件即可，以下是ZBarReaderView的初始化方法：

	ZBarReaderView readerView = [[ZBarReaderView alloc]init];
	readerView.frame = CGRectMake(0, 44, self.view.frame.size.width, 	self.view.frame.size.height - 44);
	readerView.readerDelegate = self;
//关闭闪光灯
	
	readerView.torchMode = 0;
//扫描区域
	
	CGRect scanMaskRect = CGRectMake(60, CGRectGetMidY(readerView.frame) - 126, 200, 200);

//处理模拟器
	
	if (TARGET_IPHONE_SIMULATOR) {
    	ZBarCameraSimulator *cameraSimulator 
        = [[ZBarCameraSimulator alloc]initWithViewController:self];
	    cameraSimulator.readerView = readerView;
	}
	[self.view addSubview:readerView];
//扫描区域计算
	
	readerView.scanCrop = [self getScanCrop:scanMaskRect 	readerViewBounds:self.readerView.bounds];

	[readerView start];
	
以上代码需要说明的有以下几点：

闪光灯设置
我不希望在扫描二维码时开启闪光灯，所以将ZBarReaderView的torchMode设为0，你可以将它设置为其他任何合适的值。
扫描区域计算
这点比较重要，我们常用的二维码扫描软件的有效扫描区域一般都是中央区域，其他部分是不进行扫描的，ZBar可以通过ZBarReaderView的scanCrop属性设置扫描区域，它的默认值是CGRect(0, 0, 1, 1),表示整个ZBarReaderView区域都是有效的扫描区域。我们需要把扫描区域坐标计算为对应的百度分数坐标，也就是以上代码中调用的getScanCrop:readerViewBounds方法，亲测没有问题，如下所示:

	-(CGRect)getScanCrop:(CGRect)rect readerViewBounds:(CGRect)readerViewBounds
	{
    	CGFloat x,y,width,height;

	    x = rect.origin.x / readerViewBounds.size.width;
	    y = rect.origin.y / readerViewBounds.size.height;
	    width = rect.size.width / readerViewBounds.size.width;
	    height = rect.size.height / readerViewBounds.size.height;

    	return CGRectMake(x, y, width, height);
	}

PS：在网上找到很多这个方法都是将横坐标和纵坐标交叉，这样是有问题的，仔细想一下就会明白。

初始化部分完成之后，就可以调用ZBarReaderView的start方法开始扫描了，需要让你的类实现ZBarReaderViewDelegate协议，在扫描到二维码时会调用delegate的对应方法。最后，当二维码已经识别时候，可以调用ZBarReaderView的stop方法停止扫描。如下所示:

	- (void)readerView:(ZBarReaderView *)readerView didReadSymbols:(ZBarSymbolSet *)symbols fromImage:(UIImage *)image
	{
    	for (ZBarSymbol *symbol in symbols) {
        	NSLog(@"%@", symbol.data);
	        break;
    	}

	    [self.readerView stop];
	}


zbar的使用＋为zbar添加阴影+扫描线
http://www.cnblogs.com/binglin92/p/3598753.html
