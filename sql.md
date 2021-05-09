# SQl

## Date formating 
-  Format date as 01-Feb-2019
```sql 
SELECT FORMAT (getdate(), 'dd-MMM-yyyy') as [dateHuman]
```

- Format reference [table](https://tableplus.io/blog/2018/09/sql-server-date-format-cheatsheet.html)

|Style|Standard|CONVERT statement|Output|
|--- |--- |--- |--- |
|100|Default for datetime and smalldatetime|SELECT CONVERT(VARCHAR(20), GETDATE(), 100)|mon dd yyyy hh:miAM (or PM)|
|101|U.S.|SELECT CONVERT(VARCHAR(10), GETDATE(), 101) AS [MM/DD/YYYY]|mm/dd/yyyy|
|102|ANSI|SELECT CONVERT(VARCHAR(10), GETDATE(), 102) AS [YYYY.MM.DD]|yyyy.mm.dd|
|103|British/French|SELECT CONVERT(VARCHAR(10), GETDATE(), 103) AS [DD/MM/YYYY]|dd/mm/yyyy|
|104|German|SELECT CONVERT(VARCHAR(10), GETDATE(), 104) AS [DD.MM.YYYY]|dd.mm.yyyy|
|105|Italian|SELECT CONVERT(VARCHAR(10), GETDATE(), 105) AS [DD-MM-YYYY]|dd-mm-yyyy|
|106|-|SELECT CONVERT(VARCHAR(11), GETDATE(), 106) AS [DD MON YYYY]|dd mon yyyy|
|107|-|SELECT CONVERT(VARCHAR(12), GETDATE(), 107) AS [Mon DD, YYYY]|Mon dd, yyyy|
|108|-|SELECT CONVERT(VARCHAR(8), GETDATE(), 108)|hh:mi:ss|
|109|Default + milliseconds|SELECT CONVERT(VARCHAR(26), GETDATE(), 109)|mon dd yyyy hh:mi:ss:mmmAM (or PM)|
|110|USA|SELECT CONVERT(VARCHAR(10), GETDATE(), 110) AS [MM-DD-YYYY]|mm-dd-yyyy|
|111|JAPAN|SELECT CONVERT(VARCHAR(10), GETDATE(), 111) AS [YYYY/MM/DD]|yyyy/mm/dd|
|112|ISO|SELECT CONVERT(VARCHAR(8), GETDATE(), 112) AS [YYYYMMDD]|yyyymmdd|
|113|Europe default + milliseconds|SELECT CONVERT(VARCHAR(24), GETDATE(), 113)|dd mon yyyy hh:mi:ss:mmm(24h)|
|114|-|SELECT CONVERT(VARCHAR(12), GETDATE(), 114) AS [HH:MI:SS:MMM(24H)]|hh:mi:ss:mmm(24h)|
|120|ODBC canonical|SELECT CONVERT(VARCHAR(19), GETDATE(), 120)|yyyy-mm-dd hh:mi:ss(24h)|
|121|ODBC canonical (with milliseconds) default for time, date, datetime2, and datetimeoffset|SELECT CONVERT(VARCHAR(23), GETDATE(), 121)|yyyy-mm-dd hh:mi:ss.mmm(24h)|
|126|ISO8601|SELECT CONVERT(VARCHAR(23), GETDATE(), 126)|yyyy-mm-ddThh:mi:ss.mmm (no spaces)|
|130|Hijri|SELECT CONVERT(VARCHAR(26), GETDATE(), 130)|dd mon yyyy hh:mi:ss:mmmAM|
|131|Hijra|SELECT CONVERT(VARCHAR(25), GETDATE(), 131)|dd/mm/yyyy hh:mi:ss:mmmAM|

## Case statement 
 ```sql
 select reqTime as [requestTime],
        (CASE
		WHEN [PushedToIFS] =1 THEN 'YES'
		WHEN [PushedToIFS] =0 THEN 'NO'
		WHEN Lower(LTRIM( RTRIM ([CostInvolved])))='no' THEN 'N/A'
		ELSE 'NO'
		END) AS [PushedToIFS]
	  ,[Charge] as [ChargeType]
 ```

 ## FOR Loop Function
 
 ```sql
 DECLARE @cnt INT = 0;

WHILE @cnt < cnt_total
BEGIN
   {...statements...}
   SET @cnt = @cnt + 1;
END;
 ```

 ## Array-like Function

 ```sql
 DECLARE @myTableVariable TABLE (id INT, name varchar(20))
insert into @myTableVariable values(1,'Roberto'),(2,'Gail'),(3,'Dylan')
select * from @myTableVariable where id=2
 ```

 ## Split String Function
 - for Database with compatibility level < 130

 ```sql
CREATE FUNCTION dbo.splitstring ( @stringToSplit VARCHAR(MAX) )
RETURNS
 @returnList TABLE ([Name] [nvarchar] (500))
AS
BEGIN

 DECLARE @name NVARCHAR(255)
 DECLARE @pos INT

 WHILE CHARINDEX(',', @stringToSplit) > 0
 BEGIN
  SELECT @pos  = CHARINDEX(',', @stringToSplit)  
  SELECT @name = SUBSTRING(@stringToSplit, 1, @pos-1)

  INSERT INTO @returnList 
  SELECT @name

  SELECT @stringToSplit = SUBSTRING(@stringToSplit, @pos+1, LEN(@stringToSplit)-@pos)
 END

 INSERT INTO @returnList
 SELECT @stringToSplit

 RETURN
END
 ```
 ## Compare Date instead of DateTime
 ```sql
 select * from callRecords where CAST(CallStartDateTimeGMT AS DATE)>= CAST(@CalledBetween_FromDate_param AS DATE)
 ```	
## List All dependencies
```sql
SELECT referencing_schema_name, referencing_entity_name, referencing_id, referencing_class_desc, is_caller_dependent
FROM sys.dm_sql_referencing_entities ('dbo.UserPrivilege', 'OBJECT'); --dbo.UserPrivilege is Table to be searched
```
