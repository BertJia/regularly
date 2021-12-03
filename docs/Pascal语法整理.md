### Pascal语法整理
#### 1. 报表循环数据集

````pascal
procedure Kind_IDOnEnter(Sender: TfrxComponent);begin
	basorderdtl.Close;
	basorderdtl.Open;                                                 
	OrderList.Items.Clear;                                                     
	with basorderdtl.DataSet do
	begin             
First;
while not Eof do
begin
	OrderList.Items.Add(basorderdtl.FieldByName('CUST_ID').AsString + Space3(6-length(basorderdtl.FieldByName('CUST_ID').AsString))+';  '+  basorderdtl.FieldByName('CUST_KIND').AsString +Space3(10-length(basorderdtl.FieldByName('CUST_KIND').AsString)) + basorderdtl.FieldByName('CUST_NO').AsString + Space3(10-length(basorderdtl.FieldByName('CUST_NO').AsString)) + basorderdtl.FieldByName('SHORT_NAME').AsString+ Space2(10-length(basorderdtl.FieldByName('SHORT_NAME').AsString)) + '本次单价折扣率：' +basorderdtl.FieldByName('DISCOUNT_RATE_2').AsString+ Space3(6-length(basorderdtl.FieldByName('DISCOUNT_RATE_2').AsString))+ '上次单价折扣率：' +basorderdtl.FieldByName('DISCOUNT_RATE_1').AsString);
	Next;                 
end;             
  end; 
end;
````
#### 2. 循环Checkbox
```
procedure 
var j:Integer;
begin
	begin
		for j:= CheckListBox1.Items.count-1 downto 0  do
		begin
			if CheckListBox1.Checked[j] = false then
			begin
				CheckListBox1.Items.Delete(j);                                                                                 
			end;                 
		end;                                                     
	end;
end;
```
#### 3. 想要s的从第a个开始的b个给t
想要s的从第a个开始的b个给t（即t:=copy(s,a,b)）
单引号必须成对出现，最外层的单引号表示其内部符号为字符；除最外层以外的单引号，每两个单引号代表一个'字符。
加号：+用于字符串之间的连接。字符串常量用四个单引号，例如 ' select * from TABLE where COLUMN = ''汉字'' '
字符串变量用六个单引号和两个加号，例如 ' select * from TABLE where COLUMN = '''+variable+''' '
#### 4. 获取年月日时分秒
它们的参数都是一个 TDateTime,
DateOf、TimeOf 分别提取日期与时间, 并返回 TDateTime 类型;
YearOf、MonthOf、WeekOf、DayOf、HourOf、MinuteOf、SecondOf、MilliSecondOf 返回的都是 Word 类型.
```
var dt, d, t: TDateTime;Year, Month, Week, Day, Hour, Minute, Second, MilliSecond: Word;
begin
	dt :=StrToDateTime('2009-5-20 11:22:33');
	d :=DateOf(dt);
	ShowMessage(DateToStr(d)); //2009-5-20 
	t := TimeOf(dt); 
	ShowMessage(TimeToStr(t)); //11:22:33 
	Year := YearOf(dt); 
	Month := MonthOf(dt); 
	Week := WeekOf(dt); 
	Day := DayOf(dt); 
	Hour := HourOf(dt);
	Minute := MinuteOf(dt);
	Second := SecondOf(dt);
	MilliSecond := MilliSecondOf(dt); 
	ShowMessageFmt('%d,%d,%d,%d,%d,%d,%d,%d', [Year,Month,Week,Day,Hour,Minute,Second,MilliSecond]);  // {2009,5,20,11,22,33,0}
end;

```
{DayOfWeek 获取星期几｝
``
showmessage('星期' + IntToStr(DayOfWeek(DateTimePicker1.DateTime)-1));
``
//showmessage('星期'+IntToStr(DayOfWeek(StrToDateTime('2019/04/20'))-1));

｛WeekOf获取年度周期｝
``
ShowMessage(IntToStr(WeekOf(StrToDateTime('2019/04/19'))));
``