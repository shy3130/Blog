### ios操作通讯录

为了调用系统的通讯录界面与相应功能,需要引入AddressBook.framework  
  
同时,在源文件中需要包含同文件：  


  
    #import <AddressBook/AddressBook.h>    
    #import <AddressBookUI/AddressBookUI.h>    
  
读取手机通讯录  
	
	ABAddressBookRef addressBook = ABAddressBookCreate();  
  
读取联系人 小明  
	
	CFStringRef cfName = CFSTR("小明");  
	NSArray *people = (NSArray*)ABAddressBookCopyPeopleWithName(myAddressBook, cfName);  
  
people就是名字为小明的联系人数组。默认对象是CFArray，取长度方法为：CFArrayGetCount（people）  
为了方便强制转换成了NSArray  
  
其中一个小明  
 
  
    if(people != nil && [people count]>0){    
            ABRecordRef aXiaoming0 = CFArrayGetValueAtIndex(people，0);    
    }    
        
    //获取小明0的名字    
    CFStringRef cfname = ABRecordCopyValue(aXiaoming0, kABPersonFirstNameProperty);    
        
    //获取小明0的电话信息    
    ABMultiValueRef cfphone = ABRecordCopyValue(aXiaoming0, kABPersonPhoneProperty);    
        
    //获取小明0的第0个电话类型：（比如 工作，住宅，iphone，移动电话等）    
    CFStringRef leixin = ABMultiValueCopyLabelAtIndex(cfphone,0);    
        
    //获取小明0的第3个电话号码：（使用前先判断长度ABMultiValueGetCount(cfphone)>4）    
    CFStringRef haoma = ABMultiValueCopyValueAtIndex(cfphone,3);    
        
    //添加一个联系人    
        
    CFErrorRef anError = NULL;    
    ABRecordRef aContact = ABPersonCreate();//联系人    
        
    //名字    
    NSString* name = @"小利";    
    CFStringRef cfsname = CFStringCreateWithCString( kCFAllocatorDefault, [name UTF8String], kCFStringEncodingUTF8);    
    ABRecordSetValue(aContact, kABPersonFirstNameProperty, cfsname, &anError);//写入名字进联系人    
        
    //号码    
    ABMultiValueRef phone =ABMultiValueCreateMutable(kABMultiStringPropertyType);    
    ABMultiValueAddValueAndLabel(phone, @“13800138000”,kABPersonPhoneMobileLabel, NULL);//添加移动号码0    
    ABMultiValueAddValueAndLabel(phone, @“18688888888”,kABPersonPhoneIPhoneLabel, NULL);//添加iphone号码1    
    //⋯⋯ 添加多个号码    
        
    ABRecordSetValue(aContact, kABPersonPhoneProperty, phone, &anError);//写入全部号码进联系人    
        
    ABAddressBookAddRecord(addressBook, aContact, &anError);//写入通讯录    
    ABAddressBookSave(addressBook, &error);//保存    
    //注意释放各数据    
    CFRelease(cfsname);    
    CFRelease(phone);    
    CFRelease(aContact);    
    CFRelease(addressBook);    
  
  
获取所有联系人  
  

  
    CFArrayRef allperson =ABAddressBookCopyArrayOfAllPeople(addressBook);    
    for (id person in (NSArray *)allperson) {    
    }    
  
添加联系人  

  
    //name    
     ABAddressBookRef iPhoneAddressBook = ABAddressBookCreate();    
     ABRecordRef newPerson = ABPersonCreate();    
     CFErrorRef error = NULL;    
     ABRecordSetValue(newPerson, kABPersonFirstNameProperty, firsrName.text, &error);    
     ABRecordSetValue(newPerson, kABPersonLastNameProperty, lastName.text, &error);    
     ABRecordSetValue(newPerson, kABPersonOrganizationProperty, company.text, &error);    
     ABRecordSetValue(newPerson, kABPersonFirstNamePhoneticProperty, firsrNamePY.text, &error);    
     ABRecordSetValue(newPerson, kABPersonLastNamePhoneticProperty, lastNamePY.text, &error);    
     //phone number    
     ABMutableMultiValueRef multiPhone = ABMultiValueCreateMutable(kABMultiStringPropertyType);    
     ABMultiValueAddValueAndLabel(multiPhone, houseNumber.text, kABPersonPhoneHomeFAXLabel, NULL);    
     ABMultiValueAddValueAndLabel(multiPhone, mobileNumber.text, kABPersonPhoneMobileLabel, NULL);    
     ABRecordSetValue(newPerson, kABPersonPhoneProperty, multiPhone, &error);    
     CFRelease(multiPhone);    
     //email    
     ABMutableMultiValueRef multiEmail = ABMultiValueCreateMutable(kABMultiStringPropertyType);    
     ABMultiValueAddValueAndLabel(multiEmail, email.text, kABWorkLabel, NULL);    
     ABRecordSetValue(newPerson, kABPersonEmailProperty, multiEmail, &error);    
     CFRelease(multiEmail);    
     //picture    
     NSData *dataRef = UIImagePNGRepresentation(head.image);    
     ABPersonSetImageData(newPerson, (CFDataRef)dataRef, &error);    
     ABAddressBookAddRecord(iPhoneAddressBook, newPerson, &error);    
     ABAddressBookSave(iPhoneAddressBook, &error);    
     CFRelease(newPerson);    
     CFRelease(iPhoneAddressBook);    
  
  
删除联系人  
  
  
    CFErrorRef error = NULL;    
    ABRecordRef oldPeople = ABAddressBookGetPersonWithRecordID(iPhoneAddressBook, recordID);    
    if (!oldPeople) {    
        return;    
    }    
    ABAddressBookRef iPhoneAddressBook = ABAddressBookCreate();    
    ABAddressBookRemoveRecord(iPhoneAddressBook, oldPeople, &error);    
    ABAddressBookSave(iPhoneAddressBook, &error);    
    CFRelease(iPhoneAddressBook);    
    CFRelease(oldPeople);    
  
  
获取所有组  
  
  
  
    CFArrayRef array = ABAddressBookCopyArrayOfAllGroups(iPhoneAddressBook);    
    for (id group in (NSArray *)array) {    
        NSLog(@"group name = %@", ABRecordCopyValue(group, kABGroupNameProperty));    
        NSLog(@"group id = %d", ABRecordGetRecordID(group));    
    }    
  
删除组  
  
    ABAddressBookRef iPhoneAddressBook = ABAddressBookCreate();    
    ABRecordRef oldGroup = ABAddressBookGetGroupWithRecordID(iPhoneAddressBook, RecordID);    
    ABAddressBookRemoveRecord(iPhoneAddressBook, oldGroup, nil);    
    ABAddressBookSave(iPhoneAddressBook, nil);    
    CFRelease(iPhoneAddressBook);    
    CFRelease(oldGroup);    
  
  
  
添加组  
  
    ABAddressBookRef  iPhoneAddressBook = ABAddressBookCreate();    
    ABRecordRef  newGroup = ABGroupCreate();    
    ABRecordSetValue(newGroup, kABGroupNameProperty, groupName.text, nil);    
    ABAddressBookAddRecord(iPhoneAddressBook, newGroup, nil);    
    ABAddressBookSave(iPhoneAddressBook, nil);    
    CFRelease(newGroup);    
    CFRelease(iPhoneAddressBook);    
  
  
获得通讯录中联系人的所有属性  
[html] view plaincopy  
  
    ABAddressBookRef addressBook = ABAddressBookCreate();    
     CFArrayRef results = ABAddressBookCopyArrayOfAllPeople(addressBook);    
     for(int i = 0; i < CFArrayGetCount(results); i++)    
     {    
         ABRecordRef person = CFArrayGetValueAtIndex(results, i);    
         //读取firstname    
         NSString *personName = (NSString*)ABRecordCopyValue(person, kABPersonFirstNameProperty);    
         if(personName != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"\n姓名：%@\n",personName];    
         //读取lastname    
         NSString *lastname = (NSString*)ABRecordCopyValue(person, kABPersonLastNameProperty);    
         if(lastname != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",lastname];    
         //读取middlename    
         NSString *middlename = (NSString*)ABRecordCopyValue(person, kABPersonMiddleNameProperty);    
         if(middlename != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",middlename];    
         //读取prefix前缀    
         NSString *prefix = (NSString*)ABRecordCopyValue(person, kABPersonPrefixProperty);    
         if(prefix != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",prefix];    
         //读取suffix后缀    
         NSString *suffix = (NSString*)ABRecordCopyValue(person, kABPersonSuffixProperty);    
         if(suffix != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",suffix];    
         //读取nickname呢称    
         NSString *nickname = (NSString*)ABRecordCopyValue(person, kABPersonNicknameProperty);    
         if(nickname != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",nickname];    
         //读取firstname拼音音标    
         NSString *firstnamePhonetic = (NSString*)ABRecordCopyValue(person, kABPersonFirstNamePhoneticProperty);    
         if(firstnamePhonetic != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",firstnamePhonetic];    
         //读取lastname拼音音标    
         NSString *lastnamePhonetic = (NSString*)ABRecordCopyValue(person, kABPersonLastNamePhoneticProperty);    
         if(lastnamePhonetic != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",lastnamePhonetic];    
         //读取middlename拼音音标    
         NSString *middlenamePhonetic = (NSString*)ABRecordCopyValue(person, kABPersonMiddleNamePhoneticProperty);    
         if(middlenamePhonetic != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",middlenamePhonetic];    
         //读取organization公司    
         NSString *organization = (NSString*)ABRecordCopyValue(person, kABPersonOrganizationProperty);    
         if(organization != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",organization];    
         //读取jobtitle工作    
         NSString *jobtitle = (NSString*)ABRecordCopyValue(person, kABPersonJobTitleProperty);    
         if(jobtitle != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",jobtitle];    
         //读取department部门    
         NSString *department = (NSString*)ABRecordCopyValue(person, kABPersonDepartmentProperty);    
         if(department != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",department];    
         //读取birthday生日    
         NSDate *birthday = (NSDate*)ABRecordCopyValue(person, kABPersonBirthdayProperty);    
         if(birthday != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",birthday];    
         //读取note备忘录    
         NSString *note = (NSString*)ABRecordCopyValue(person, kABPersonNoteProperty);    
         if(note != nil)    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",note];    
         //第一次添加该条记录的时间    
         NSString *firstknow = (NSString*)ABRecordCopyValue(person, kABPersonCreationDateProperty);    
         NSLog(@"第一次添加该条记录的时间%@\n",firstknow);    
         //最后一次修改該条记录的时间    
         NSString *lastknow = (NSString*)ABRecordCopyValue(person, kABPersonModificationDateProperty);    
         NSLog(@"最后一次修改該条记录的时间%@\n",lastknow);    
             
         //获取email多值    
         ABMultiValueRef email = ABRecordCopyValue(person, kABPersonEmailProperty);    
         int emailcount = ABMultiValueGetCount(email);    
         for (int x = 0; x < emailcount; x++)    
         {    
             //获取email Label    
             NSString* emailLabel = (NSString*)ABAddressBookCopyLocalizedLabel(ABMultiValueCopyLabelAtIndex(email, x));    
             //获取email值    
             NSString* emailContent = (NSString*)ABMultiValueCopyValueAtIndex(email, x);    
             textView.text = [textView.text stringByAppendingFormat:@"%@:%@\n",emailLabel,emailContent];    
         }    
         //读取地址多值    
         ABMultiValueRef address = ABRecordCopyValue(person, kABPersonAddressProperty);    
         int count = ABMultiValueGetCount(address);    
             
         for(int j = 0; j < count; j++)    
         {    
             //获取地址Label    
             NSString* addressLabel = (NSString*)ABMultiValueCopyLabelAtIndex(address, j);    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",addressLabel];    
             //获取該label下的地址6属性    
             NSDictionary* personaddress =(NSDictionary*) ABMultiValueCopyValueAtIndex(address, j);    
             NSString* country = [personaddress valueForKey:(NSString *)kABPersonAddressCountryKey];    
             if(country != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"国家：%@\n",country];    
             NSString* city = [personaddress valueForKey:(NSString *)kABPersonAddressCityKey];    
             if(city != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"城市：%@\n",city];    
             NSString* state = [personaddress valueForKey:(NSString *)kABPersonAddressStateKey];    
             if(state != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"省：%@\n",state];    
             NSString* street = [personaddress valueForKey:(NSString *)kABPersonAddressStreetKey];    
             if(street != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"街道：%@\n",street];    
             NSString* zip = [personaddress valueForKey:(NSString *)kABPersonAddressZIPKey];    
             if(zip != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"邮编：%@\n",zip];    
             NSString* coutntrycode = [personaddress valueForKey:(NSString *)kABPersonAddressCountryCodeKey];    
             if(coutntrycode != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"国家编号：%@\n",coutntrycode];    
         }    
             
         //获取dates多值    
         ABMultiValueRef dates = ABRecordCopyValue(person, kABPersonDateProperty);    
         int datescount = ABMultiValueGetCount(dates);    
         for (int y = 0; y < datescount; y++)    
         {    
             //获取dates Label    
             NSString* datesLabel = (NSString*)ABAddressBookCopyLocalizedLabel(ABMultiValueCopyLabelAtIndex(dates, y));    
             //获取dates值    
             NSString* datesContent = (NSString*)ABMultiValueCopyValueAtIndex(dates, y);    
             textView.text = [textView.text stringByAppendingFormat:@"%@:%@\n",datesLabel,datesContent];    
         }    
         //获取kind值    
         CFNumberRef recordType = ABRecordCopyValue(person, kABPersonKindProperty);    
         if (recordType == kABPersonKindOrganization) {    
             // it's a company    
             NSLog(@"it's a company\n");    
         } else {    
             // it's a person, resource, or room    
             NSLog(@"it's a person, resource, or room\n");    
         }    
             
             
         //获取IM多值    
         ABMultiValueRef instantMessage = ABRecordCopyValue(person, kABPersonInstantMessageProperty);    
         for (int l = 1; l < ABMultiValueGetCount(instantMessage); l++)    
         {    
             //获取IM Label    
             NSString* instantMessageLabel = (NSString*)ABMultiValueCopyLabelAtIndex(instantMessage, l);    
             textView.text = [textView.text stringByAppendingFormat:@"%@\n",instantMessageLabel];    
             //获取該label下的2属性    
             NSDictionary* instantMessageContent =(NSDictionary*) ABMultiValueCopyValueAtIndex(instantMessage, l);    
             NSString* username = [instantMessageContent valueForKey:(NSString *)kABPersonInstantMessageUsernameKey];    
             if(username != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"username：%@\n",username];    
                 
             NSString* service = [instantMessageContent valueForKey:(NSString *)kABPersonInstantMessageServiceKey];    
             if(service != nil)    
                 textView.text = [textView.text stringByAppendingFormat:@"service：%@\n",service];    
         }    
             
         //读取电话多值    
         ABMultiValueRef phone = ABRecordCopyValue(person, kABPersonPhoneProperty);    
         for (int k = 0; k<ABMultiValueGetCount(phone); k++)    
         {    
             //获取电话Label    
             NSString * personPhoneLabel = (NSString*)ABAddressBookCopyLocalizedLabel(ABMultiValueCopyLabelAtIndex(phone, k));    
             //获取該Label下的电话值    
             NSString * personPhone = (NSString*)ABMultiValueCopyValueAtIndex(phone, k);    
                 
             textView.text = [textView.text stringByAppendingFormat:@"%@:%@\n",personPhoneLabel,personPhone];    
         }    
             
         //获取URL多值    
         ABMultiValueRef url = ABRecordCopyValue(person, kABPersonURLProperty);    
         for (int m = 0; m < ABMultiValueGetCount(url); m++)    
         {    
             //获取电话Label    
             NSString * urlLabel = (NSString*)ABAddressBookCopyLocalizedLabel(ABMultiValueCopyLabelAtIndex(url, m));    
             //获取該Label下的电话值    
             NSString * urlContent = (NSString*)ABMultiValueCopyValueAtIndex(url,m);    
                 
             textView.text = [textView.text stringByAppendingFormat:@"%@:%@\n",urlLabel,urlContent];    
         }    
             
         //读取照片    
         NSData *image = (NSData*)ABPersonCopyImageData(person);    
             
             
         UIImageView *myImage = [[UIImageView alloc] initWithFrame:CGRectMake(200, 0, 50, 50)];    
         [myImage setImage:[UIImage imageWithData:image]];    
         myImage.opaque = YES;    
         [textView addSubview:myImage];    
             
             
             
     }    
         
     CFRelease(results);    
     CFRelease(addressBook); 
