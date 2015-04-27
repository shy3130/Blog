### 如何检测iOS里安装的其它软件？  

  
  NSURL *instagramURL = [NSURL URLWithString:@"instagram://location?id=1"];
  BOOL hasInstagram = [[UIApplication sharedApplication] canOpenURL:instagramURL]; 

http://www.cocoachina.com/bbs/read.php?tid=131788





#### 唐巧：
### 如何检测iOS里安装的其它软件？  

http://tangqiaoboy.blog.163.com/blog/static/116114258201172975359440/



###cocoachaina
Bump 有一个小功能是给你身边的人交换应用程序；但是iOS是没有接口提供开发者去获取用户手机所安装的app的，但是，为什么Bump这款通过正规渠道（Appstore）下载的应用却能够获取用户安装应用清单呢？原来，我们还是有一些绕弯的方法来获得用户安装的软件的。网址：http://amitay.us/blog/files/how_to_detect_installed_ios_apps.php  中列出了4种用于检测用户安装的软件的方法：

  方法一：http://developer.apple.com/library/ios/#featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html
  方法二：http://forrst.com/posts/UIDevice_Category_For_Processes-h1H
  方法三：http://stackoverflow.com/questions/3878197/is-it-possible-to-get-information-about-all-apps-installed-on-iphone/3878220#3878220
  方法四：http://www.iphonedevsdk.com/forum/iphone-sdk-development/22289-possible-retrieve-these-information.html

其中最后2种是私有API和只适用于jail break的iOS设备，而前2种适用于普通的iOS设备。大概解释一下前2种方法：
  方法一：利用URL scheme，看对于某一应用特有的url scheme，有没有响应。如果有响应，就说明安装了这个特定的app。
  方法二：利用一些方法获得当前正在运行的进程信息，从进程信息中获得安装的app信息。


http://tangqiaoboy.blog.163.com/blog/static/116114258201172975359440/
