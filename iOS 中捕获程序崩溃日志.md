### iOS 中捕获程序崩溃日志
iOS开发中遇到程序崩溃是很正常的事情，如何在程序崩溃时捕获到异常信息并通知开发者，是大多数软件都选择的方法。下面就介绍如何在iOS中实现：

在程序启动时加上一个异常捕获监听，用来处理程序崩溃时的回调动作
 	
 	
 	 NSSetUncaughtExceptionHandler (&UncaughtExceptionHandler);
  官方文档介绍：`Sets the top-level error-handling function where you can perform last-minute logging before the program terminates.`
  UncaughtExceptionHandler是一个函数指针，该函数需要我们实现，可以取自己想要的名字。当程序发生异常崩溃时，该函数会得到调用，这跟C，C++中的回调函数的概念是一样的。



实现自己的处理函数
		
	-(void) UncaughtExceptionHandler(NSException *exception) {
   
   		    NSArray *arr = [exception callStackSymbols];//得到当前调用栈信息
    		NSString *reason = [exception reason];//非常重要，就是崩溃的原因
    		NSString *name = [exception name];//异常类型
   
    		NSLog(@"exception type : %@ \n crash reason : %@ \n call stack info : %@", name, reason, arr);
	}

以上代码很简单，但是带来的作用是非常大的。

__获取到了崩溃的日子，如何发送给开发者呢，目前一般有以下两种方式：
 将崩溃信息持久化在本地，下次程序启动时，将崩溃信息作为日志发送给开发者。__

 通过邮件发送给开发者。 不过此种方式需要得到用户的许可，因为iOS不能后台发送短信或者邮件，会弹出发送邮件的界面，只有用户点击了发送才可发送。 不过，此种方式最符合苹果的以用户至上的原则。
发送邮件代码也很简单：
		
		 NSString *crashLogInfo = [NSString stringWithFormat:@"exception type : %@ \n crash reason : %@ \n call stack info : %@", name, reason, arr];
		 
   		 NSString *urlStr = [NSString stringWithFormat:@"mailto://tianranwuwai@yeah.net?subject=bug报告&body=感谢您的"错误详情:%@",crashLogInfo];
  	  NSURL *url = [NSURL URLWithString:[urlStr stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
   	 [[UIApplication sharedApplication] openURL:url];

以上就是iOS中捕获异常常用的方法，大家可以不妨一试！

