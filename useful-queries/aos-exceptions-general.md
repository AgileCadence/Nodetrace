# AOS Exceptions - General

## Researching Queries

Get the exceptions thrown in the selected time period and trim stack trace:

```sql
AppExceptions 
| project TimeGenerated, Message = split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0), Stack = split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',1), UserId, AppRoleInstance
```

Get the top 10 exceptions:

```sql
AppExceptions 
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
| top 10 by count_
```

Get empty message as this shows potential uncaptured errors:

```sql
AppExceptions 
| where strlen(tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )) == 4 
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',1) )
```

Which AOS throws the most exceptions?:

```sql
AppExceptions 
| where ClientType == "PC"
| summarize count() by AppRoleInstance
```

Which jobs throw the most exceptions on the worst AOS?:

```sql
AppExceptions 
| where ClientType == "PC" and AppRoleInstance == "<CLOUDROLEINSTANCE NAME HERE>"
| summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
| order by count_ desc 
```

