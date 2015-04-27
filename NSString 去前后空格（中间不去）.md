### NSString 去前后空格（中间不去）

delayAlertView.textView.text = [delayAlertView.textView.text stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
