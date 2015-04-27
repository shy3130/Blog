### iOS 摄像头

#iOS摄像头和相册-UIImagePickerController-浅析  



    

在一些应用中，我们需要用到iOS设备的摄像头进行拍照，视频。并且从相册中选取我们需要的图片或者视频。
关于iOS摄像头和相册的应用，可以使用`UIImagePickerController`类来完成控制。
关于`UIImagePickerControlle`r的相关知识，
如下：

iOS的一些设备上都安装了摄像头。现在绝大多数都有了。
在编程中，我们是用相应的东西来进行照相，录像等功能。

一、UIImagePickerController类
UIImagePickerController 这个类可以为大家提供照相的功能,以及图片,视频浏览的功能。 


二、检查硬件是否安装有摄像头或者允许操作相册

这些公共的方法，我们也许会用到，我就贴了！So easy！！！

###pragma mark - 摄像头和相册相关的公共类

// 判断设备是否有摄像头
	
	- (BOOL) isCameraAvailable{
 	   return [UIImagePickerController isSourceTypeAvailable:UIImagePickerControllerSourceTypeCamera];
	}

// 前面的摄像头是否可用
	
	- (BOOL) isFrontCameraAvailable{
   	 return [UIImagePickerControllerisCameraDeviceAvailable:UIImagePickerControllerCameraDeviceFront];
	}

// 后面的摄像头是否可用
	
	- (BOOL) isRearCameraAvailable{
    	return 	[UIImagePickerControllerisCameraDeviceAvailable:UIImagePickerControllerCameraDeviceRear];
	}


// 判断是否支持某种多媒体类型：拍照，视频
	
	- (BOOL) cameraSupportsMedia:(NSString *)paramMediaType sourceType:(UIImagePickerControllerSourceType)paramSourceType{
    __block BOOL result = NO;
    if ([paramMediaType length] == 0){
        NSLog(@"Media type is empty.");
        return NO;
    }
    NSArray *availableMediaTypes =[UIImagePickerControlleravailableMediaTypesForSourceType:paramSourceType];
    [availableMediaTypes enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL*stop) {
                                                        NSString *mediaType = (NSString *)obj;
                                                        if ([mediaTypeisEqualToString:paramMediaType]){
                                                            result = YES;
                                                            *stop= YES;
                                                        }
        
    }];
    return result;
}

// 检查摄像头是否支持录像
	
	- (BOOL) doesCameraSupportShootingVideos{
    return [self cameraSupportsMedia:( NSString *)kUTTypeMoviesourceType:UIImagePickerControllerSourceTypeCamera];
}

// 检查摄像头是否支持拍照
	
	- (BOOL) doesCameraSupportTakingPhotos{
    return [self cameraSupportsMedia:( NSString *)kUTTypeImagesourceType:UIImagePickerControllerSourceTypeCamera];
}

###pragma mark - 相册文件选取相关
// 相册是否可用
	
	- (BOOL) isPhotoLibraryAvailable{
    return [UIImagePickerController isSourceTypeAvailable: UIImagePickerControllerSourceTypePhotoLibrary];
}

// 是否可以在相册中选择视频
	
	- (BOOL) canUserPickVideosFromPhotoLibrary{
    return [self cameraSupportsMedia:( NSString *)kUTTypeMovie sourceType:UIImagePickerControllerSourceTypePhotoLibrary];
}

// 是否可以在相册中选择视频
	
	- (BOOL) canUserPickPhotosFromPhotoLibrary{
    return [self cameraSupportsMedia:( NSString *)kUTTypeImage sourceType:UIImagePickerControllerSourceTypePhotoLibrary];
}

三、用摄像头进行拍照和录像功能

1.我们将UIImagePickerController功能写在一个按钮的点击事件中：

 
###pragma mark - 拍照按钮事件

	
	- (void)ClickControlAction:(id)sender{
    // 判断有摄像头，并且支持拍照功能
    if ([self isCameraAvailable] && [self doesCameraSupportTakingPhotos]){
        // 初始化图片选择控制器
        UIImagePickerController *controller = [[UIImagePickerController alloc] init];
        [controller setSourceType:UIImagePickerControllerSourceTypeCamera];// 设置类型

        
        // 设置所支持的类型，设置只能拍照，或则只能录像，或者两者都可以
        NSString *requiredMediaType = ( NSString *)kUTTypeImage;
        NSString *requiredMediaType1 = ( NSString *)kUTTypeMovie;
        NSArray *arrMediaTypes=[NSArray arrayWithObjects:requiredMediaType, requiredMediaType1,nil];
        [controller setMediaTypes:arrMediaTypes];
        
        // 设置录制视频的质量
        [controller setVideoQuality:UIImagePickerControllerQualityTypeHigh];
        //设置最长摄像时间
        [controller setVideoMaximumDuration:10.f];
        

        [controller setAllowsEditing:YES];// 设置是否可以管理已经存在的图片或者视频
        [controller setDelegate:self];// 设置代理
        [self.navigationController presentModalViewController:controller animated:YES];
        [controller release];
    } else {
        NSLog(@"Camera is not available.");
    }
	}


解释：

`setSourceType`方法

通过设置setSourceType方法可以确定调用出来的`UIImagePickerController`所显示出来的界面

	
	typedef NS_ENUM(NSInteger, UIImagePickerControllerSourceType) {
    UIImagePickerControllerSourceTypePhotoLibrary,
    UIImagePickerControllerSourceTypeCamera,
    UIImagePickerControllerSourceTypeSavedPhotosAlbum
	};

分别表示：图片列表，摄像头，相机相册

`setMediaTypes`方法

// 设置所支持的类型，设置只能拍照，或则只能录像，或者两者都可以
	
	        NSString *requiredMediaType = ( NSString *)kUTTypeImage;
        NSString *requiredMediaType1 = ( NSString *)kUTTypeMovie;
        NSArray *arrMediaTypes=[NSArray arrayWithObjects:requiredMediaType, requiredMediaType1,nil];
        [controller setMediaTypes:arrMediaTypes];


4.关于`UIImagePickerControllerDelegate`协议

我们要对我们拍摄的照片和视频进行存储，那么就要实现UIImagePickerControllerDelegate协议的方法。

###pragma mark - UIImagePickerControllerDelegate 代理方法


// 保存图片后到相册后，调用的相关方法，查看是否保存成功
	
	- (void) imageWasSavedSuccessfully:(UIImage *)paramImage didFinishSavingWithError:(NSError *)paramError contextInfo:(void *)paramContextInfo{
    if (paramError == nil){
        NSLog(@"Image was saved successfully.");
    } else {
        NSLog(@"An error happened while saving the image.");
        NSLog(@"Error = %@", paramError);
    }
	}

// 当得到照片或者视频后，调用该方法
-(void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info{
    NSLog(@"Picker returned successfully.");
    NSLog(@"%@", info);
    NSString *mediaType = [info objectForKey:UIImagePickerControllerMediaType];
    // 判断获取类型：图片
    if ([mediaType isEqualToString:( NSString *)kUTTypeImage]){
        UIImage *theImage = nil;
        // 判断，图片是否允许修改
        if ([picker allowsEditing]){
            //获取用户编辑之后的图像
            theImage = [info objectForKey:UIImagePickerControllerEditedImage];
        } else {
            // 照片的元数据参数
            theImage = [info objectForKey:UIImagePickerControllerOriginalImage];
            
        }
        
        // 保存图片到相册中
        SEL selectorToCall = @selector(imageWasSavedSuccessfully:didFinishSavingWithError:contextInfo:);
        UIImageWriteToSavedPhotosAlbum(theImage, self,selectorToCall, NULL);
        
    }else if ([mediaType isEqualToString:(NSString *)kUTTypeMovie]){
        // 判断获取类型：视频
        //获取视频文件的url
        NSURL* mediaURL = [info objectForKey:UIImagePickerControllerMediaURL];
        //创建ALAssetsLibrary对象并将视频保存到媒体库
        // Assets Library 框架包是提供了在应用程序中操作图片和视频的相关功能。相当于一个桥梁，链接了应用程序和多媒体文件。
        ALAssetsLibrary *assetsLibrary = [[ALAssetsLibrary alloc] init];
        // 将视频保存到相册中
        [assetsLibrary writeVideoAtPathToSavedPhotosAlbum:mediaURL
                                          completionBlock:^(NSURL *assetURL, NSError *error) {
                                              if (!error) {
                                                  NSLog(@"captured video saved with no error.");
                                              }else{
                                                  NSLog(@"error occured while saving the video:%@", error);
                                              }
                                          }];
        [assetsLibrary release];
    
    
    }
    
    
    [picker dismissModalViewControllerAnimated:YES];
}



// 当用户取消时，调用该方法
	
	- (void)imagePickerControllerDidCancel:(UIImagePickerController *)picker{
    
    [picker dismissModalViewControllerAnimated:YES];
	}

四、从相册获取图片和视频数据

1.我们将功能封装在一个按钮的点击事件中

###pragma mark - 相册操作

	
	- (void)ClickShowPhotoAction:(id)sender{
    
    if ([self isPhotoLibraryAvailable]){
        UIImagePickerController *controller = [[UIImagePickerController alloc] init];
         [controller setSourceType:UIImagePickerControllerSourceTypePhotoLibrary];// 设置类型
        NSMutableArray *mediaTypes = [[NSMutableArray alloc] init];
        if ([self canUserPickPhotosFromPhotoLibrary]){
            [mediaTypes addObject:( NSString *)kUTTypeImage];
        }
        if ([self canUserPickVideosFromPhotoLibrary]){
            [mediaTypes addObject:( NSString *)kUTTypeMovie];
        }
        
        [controller setMediaTypes:mediaTypes];
        [controller setDelegate:self];// 设置代理
        [self.navigationController presentModalViewController:controller animated:YES];
        [controller release];
        [mediaTypes release];
        
    	}


	}

2.关于`UIImagePickerControllerDelegate`协议，我们可以重用。

在这里，就不用赘述了！

最后，需要说的是，UIImagePickerControllerDelegate协议中
	
	-(void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info方法，中的info值，会根据我们操作的类型不同，而产生了不同的数据信息：

当操作的为图片时：：

	
		
		 
	
	{
    UIImagePickerControllerCropRect = "NSRect: {{0, 405}, {2448, 2449}}";
    UIImagePickerControllerEditedImage = "";
    UIImagePickerControllerMediaMetadata =     {
        DPIHeight = 72;
        DPIWidth = 72;
        Orientation = 6;
        "{Exif}" =         {
            ApertureValue = "2.526068811667588";
            BrightnessValue = "-0.0709875088566263";
            ColorSpace = 1;
            DateTimeDigitized = "2013:04:05 16:43:00";
            DateTimeOriginal = "2013:04:05 16:43:00";
            ExposureMode = 0;
            ExposureProgram = 2;
            ExposureTime = "0.05882352941176471";
            FNumber = "2.4";
            Flash = 24;
            FocalLenIn35mmFilm = 35;
            FocalLength = "4.28";
            ISOSpeedRatings =             (
                400
            );
            MeteringMode = 5;
            PixelXDimension = 3264;
            PixelYDimension = 2448;
            SceneType = 1;
            SensingMethod = 2;
            Sharpness = 0;
            ShutterSpeedValue = "4.099543917546131";
            SubjectArea =             (
                1631,
                1223,
                881,
                881
            );
            WhiteBalance = 0;
        };
        "{TIFF}" =         {
            DateTime = "2013:04:05 16:43:00";
            Make = Apple;
            Model = "iPhone 4S";
            Software = "5.1.1";
            XResolution = 72;
            YResolution = 72;
        };
    };
    UIImagePickerControllerMediaType = "public.image";
    UIImagePickerControllerOriginalImage = "";
	}

当我们操作的为视频时：

	
	{
   	 	UIImagePickerControllerMediaType = "public.movie";
    	UIImagePickerControllerMediaURL = "file://localhost/private/var/mobile/Applications/22A14825-DD7E-48E1-A1D5-2D85B82095B5/tmp/capture-T0x1363a0.tmp.etXfD4/capturedvideo.MOV";
	}

希望对你有所帮助！
http://yul100887.blog.163.com/blog/static/20033613520141243289426/
