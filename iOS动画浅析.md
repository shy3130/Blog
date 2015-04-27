## iOS动画浅析

####在iOS中动画实现技术主要是：Core Animation

 `Core Animation`负责所有的滚动、旋转、缩小和放大以及所有的iOS动画效果。其中UIKit类通常都有animated：参数部分，它可以允许是否使用动画。 

####Core Animation主要是使用

我们知道每个UIView都关联到一个CALayer对象，CALayer是Core Animation中的图层。

Core Animation主要就是通过修改图层来改变UI的大小，位置，从而实现动画效果。


可以说，任何一个应用程序都离不开动画！
就连苹果各个UI控件中的切换操作，都有它内在的动画。

了解一下，关于动画的一些知识。

任何知识点，都会迁出一系列的知识点。

	
	[UIView beginAnimations:@"dropDownloadLabel" context:UIGraphicsGetCurrentContext()];
	[UIView setAnimationDuration: 0.5];
	[UIView setAnimationBeginsFromCurrentState: NO];

// 执行的动画code
        
	[UIView commitAnimations];

就将这段代码作为知识的切入点，开始了解吧。

	[UIView beginAnimations:@"dropDownloadLabel" context:UIGraphicsGetCurrentContext()];
	[UIView commitAnimations];

这两句代码，标记了一个动画的开始和结束。在中间我们可以写我们的一些动画操作！

`beginAnimations`方法

	+ (void)beginAnimations:(NSString *)animationID context:(void *)context
用来，表示动画的开始。
animationID：作为动画的标识
context：自定义的一些动画数据，这些数据将发送给动画的代理方法：setAnimationWillStartSelector:方法和setAnimationDidStopSelector:方法。
这个，参数，通常为nil。我们可以直接设置为nil。
这里，我们使用UIGraphicsGetCurrentContext()；因为此方法默认也会返回nil。

该方法告诉系统，我们将开始动画。并且，在该方法后，我们可以通过setAnimationXXX（一系列方法）来设置我们进行的动画的一些参数。
完成动画后，调用commitAnimations方法来通知系统，动画结束。

至此，我们知道，就是设置动画的一些列参数的方法即setAnimationXXX方法。

	[UIView setAnimationDuration: 0.5];
	[UIView setAnimationBeginsFromCurrentState: NO];


动画是可以嵌套的。
	
	[UIView beginAnimations:@"animation_1" context:UIGraphicsGetCurrentContext()];
	// code1
	[UIView beginAnimations:@"animation_2" context:UIGraphicsGetCurrentContext()];
	// code2
	[UIView commitAnimations];

	[UIView commitAnimations];


如果我们为动画设置了，setAnimationWillStartSelector:方法和setAnimationDidStopSelector:方法。
那么当动画开始或者停止的时候，动画的animationID参数和context参数，会传递给setAnimationWillStartSelector:方法和setAnimationDidStopSelector:方法。


__悲剧总是要发生的！__

苹果API在最后的描述中，给了这么一句话：

`Use of this method is discouraged in iOS 4.0 and later. You should use the block-based animation methods to specify your animations instead.`	

可见，在iOS 4.0 后，block语法，大大增多了。这种方式，是不建议的，需要我们使用block的方式。

于是，动画的block方式：

	[UIView animateWithDuration:0.3f delay:0.0f options:UIViewAnimationOptionCurveLinear
                             animations:^{ // 执行的动画code}
                             completion:^(BOOL finished){
                                 // 完成后执行code
                             }];



在尽量用`block`来完成动画，因为说不定啥时候，老的动画方式，将被废除。

到此，可以告一段落。但是，我想将这简单的动画代码，一查到底！


commitAnimations方法：
	
	+ (void)commitAnimations

标记动画结束。与beginAnimations方法成对使用。
例如：
	
	[UIView commitAnimations];

一系列的setAnimationXXX方法：

	setAnimationDuration方法：

+ (void)setAnimationDuration:(NSTimeInterval)duration

设置动画持续时间（秒）

例如：
	
	[UIView setAnimationDuration: 0.5];


setAnimationBeginsFromCurrentState方法
	
	+ (void)setAnimationBeginsFromCurrentState:(BOOL)fromCurrentState

设置动画开始时的状态。

我们构想一个场景：一般，我们按下一个按钮，将会执行动画一次。

当YES时：当上一次动画正在执行中，那么当下一个动画开始时，上一次动画的当前状态将成为下一次动画的开始状态。
当NO时：当上一个动画正在执行中，那么当下一个动画开始时，上一次动画需要先恢复到完成时的状态，然后在开始执行下一次动画。

setAnimationStartDate方法

	+ (void)setAnimationStartDate:(NSDate *)startTime

设置动画开始时间。

setAnimationDelay方法

	+ (void)setAnimationDelay:(NSTimeInterval)delay

设置动画开始的延迟时间（秒）。

setAnimationCurve方法

	+ (void)setAnimationCurve:(UIViewAnimationCurve)curve

设置动画的曲线方式（就是动画的总体变化的时间曲线：开始快最后慢，开始慢最后快，最后慢，均匀线性）。

curve参数如下：

	typedef NS_ENUM(NSInteger, UIViewAnimationCurve) {
        UIViewAnimationCurveEaseInOut,         // slow at beginning and end
        UIViewAnimationCurveEaseIn,            // slow at beginning
        UIViewAnimationCurveEaseOut,           // slow at end
        UIViewAnimationCurveLinear
    };


setAnimationRepeatCount方法

	+ (void)setAnimationRepeatCount:(float)repeatCount

设置动画重复次数

setAnimationRepeatAutoreverses方法

	+ (void)setAnimationRepeatAutoreverses:(BOOL)repeatAutoreverses

设置动画是否做一次反向的执行。
如果设置为YES：动画将执行：动画初始状态》动画》动画完成状态》动画》动画初始状态。
如果设置为NO：默认值

setAnimationsEnabled方法

	+ (void)setAnimationsEnabled:(BOOL)enabled

设置动画是否可用！
YES：默认值。
NO：动画效果被禁用
注意：仅仅是动画是否可用，在动画中被改变的UI对象依然是起作用的。仅仅是动画效果被禁用了。

areAnimationsEnabled方法

+ (BOOL)areAnimationsEnabled

返回动画效果是否被禁用。

提倡使用block方式来进行更加多的，简洁的控制！

其实发现了，这篇博客已经有点长了！有点坏味道！
不过，回头看，既然开篇就提到了Core Animation！
苹果中，默认的的简单动画，可以用setAnimationXXX一类的方法。但是如果，要让动画更加美观，复杂，那我想就要考Core Animation了！
####做个分割，以下了解Core Animation！
------------------------------------------------------------------------------------------------------------------------------------

先来个代码吧！

 
	[_imgPic setImage:image];// 设置新的图片
            
           
            CATransition *animation = [CATransition animation];
            [animation setDuration:1.0];
            [animation setFillMode:kCAFillModeForwards];
            [animation setTimingFunction:[CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut]];
            [animation setType:@"rippleEffect"];// rippleEffect
            [animation setSubtype:kCATransitionFromTop];
            [_imgPic.layer addAnimation:animation forKey:nil];

一头茫然啊！

`UIView`和`CATransition`两种动画是什么关系？到底用哪一种呢？

 
一种是UIView层面的。

一种是使用CATransition进行更低层次的控制。

第一种是UIView，UIView方式可能在低层也是使用CATransition进行了封装，它只能用于一些简单的、常用的效果展现。

所以，两者，往往在需要复杂的动画，应该用CATransition吧。




希望对你有所帮助！
http://blog.sina.com.cn/s/blog_7b9d64af0101b8nh.html

## IOS 30多个iOS常用动画，带详细注释  

http://shiminghua234.blog.163.com/blog/static/26391242201341633018353/?COLLCC=2124738988&
