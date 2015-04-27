####列举两个场景对比一下，也许tableviewcell的复用就很清晰明了了。
1，
	
	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath*)indexPath{

    static NSString *CellIdentifier = @"cell1";
    UITableViewCell *cell  =[tableView dequeueReusableCellWithIdentifier:CellIdentifier];

    if (cell == nil) {

       cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];

	UILabel *labelTest = [[UILabel alloc]init];

	 [labelTest setFrame:CGRectMake(2, 2, 80, 40)];

     [labelTest setBackgroundColor:[UIColor clearColor]];

	[labelTest setTag:1];

	[[cell contentView]addSubview:labelTest];

	}

	UILabel *label1 = (UILabel*)[cell viewWithTag:1];

	[label1 setText:[self.tests objectAtIndex:indexPath.row]];

	return cell;

	}

2，
	
	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath*)indexPath{

    static NSString *CellIdentifier = @"cell1";
    UITableViewCell *cell  =[tableView dequeueReusableCellWithIdentifier:CellIdentifier];

    if (cell == nil) {

        cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]; 

	 }

    UILabel *labelTest = [[UILabel alloc]init];

    [labelTest setFrame:CGRectMake(2, 2, 80, 40)];

    [labelTest setBackgroundColor:[UIColor clearColor]];  //之所以这里背景设为透明，就是为了后面让大家看到cell上叠加的label。

    [labelTest setTag:1];

    [[cell contentView]addSubview:labelTest];

    [labelTest setText:[self.tests objectAtIndex:indexPath.row]];

	return cell;
	}
#####当你上下来回滑动tableview的时候就会看到区别，第一种程序界面不会出现异常，但是第二种就不是了，会出现字体叠加现象，其实更确切的是多个label的叠加。为什么呢，因为在tableview刷新的时候，如果那个位置已经有现成的cell，它就不会再重新请求资源生成新的cell了，而是复用原来的cell。所以对于对于第一种，代码的思路是第一次在cell不存在的时候生成cell，定义cell样式，以后不管是刷新还是重新请求还好，它都只是复用已生成的cell。而第二种思路是，在cell不存在的时候，请求生成cell，然后给cell上添加label，刷新的时候，会复用已有的cell，但是会重复添加label，故造成重叠的现象。

__之前类似的问题来回困扰了我好多次，我都没有下决心彻底搞清楚，每次都是得过且过，只要程序最好调好了，就OK。今天又碰到了类似的问题，终于大致搞清楚了，希望以后不会再被它坑害。__
