###在iphone程序中打开word、execl、pdf等文档   

方法一：
用UIWebView就可以了
	
	-(void)loadDocument:(NSString*)documentName inView:(UIWebView*)webView
	{
    	NSString *path = [[NSBundle mainBundle] pathForResource:documentName ofType:nil];
	    NSURL *url = [NSURL fileURLWithPath:path];
    	NSURLRequest *request = [NSURLRequest requestWithURL:url];
	    [webView loadRequest:request];
	}

	// Calling -loadDocument:inView:
	[self loadDocument:@"test.doc" inView:self.myWebview];


方法我也已经测试过了，希望对大家有帮助，


方法二：
下面方法是直接通过QLPreviewController打开文档

	qlViewController = [[QLPreviewController alloc] init];
	   qlViewController.dataSource = self;  
	   [self presentModalViewController:qlViewController animated:YES];


	- (NSInteger)numberOfPreviewItemsInPreviewController:(QLPreviewController *)controller {
	return 1;
	}
	- (id <QLPreviewItem>)previewController:(QLPreviewController *)controller 
      previewItemAtIndex:(NSInteger)index{
	//-------------读文件
	NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
	NSString *documentsDirectory = [paths objectAtIndex:0];
	if (!documentsDirectory) {
	  NSLog(@"Documents directory not found!");//return ;
	} 
	NSString *fileName=[NSString stringWithFormat:@"%@.%@",nameQ,extQ];
	NSString *appFile = [documentsDirectory stringByAppendingPathComponent:fileName]; 
	//-------------
	NSURL *myQLDocument = [NSURL fileURLWithPath:appFile];
	return myQLDocument;
	}


