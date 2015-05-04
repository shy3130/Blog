###UIView转化成UIImage
	
	- (UIImage*) imageWithUIView:(UIView*) view
	{    
    	UIGraphicsBeginImageContext(view.bounds.size);
    	CGContextRef ctx = UIGraphicsGetCurrentContext();
    	[view.layer renderInContext:ctx];
    	UIImage* tImage = UIGraphicsGetImageFromCurrentImageContext();
    	UIGraphicsEndImageContext();
	    return tImage;
	}
	
这个方法这样调用就可以：
	
	UIImage *image = [self imageWithUIView:label];//将label转化成UIImage
	
	
在此方法中如果是`UILabel`转化成`UIImage`，则转化完成后的Image上面的文字会比较模糊。
解决的办法就是增加控件创建时的frame（宽高变4倍）。这样在压缩转化之后就会看着很自然呢。
