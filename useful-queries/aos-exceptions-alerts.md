# AOS Exceptions - Alerts

## Key Queries

Get SQL Connection Errors:

```sql
AppExceptions 
| where OuterMessage contains "SQL"
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
```

Get DMF errors:

```sql
AppExceptions 
| where OuterMessage contains "DMF"
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
```

Get ODBC errors:

```sql
AppExceptions 
| where OuterMessage contains "ODBC"
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
```

Get BYOD errors:

```sql
AppExceptions 
| where OuterMessage contains "BYOD"
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
```

Get XML errors:

```sql
AppExceptions 
| where OuterMessage contains "XML-protocol"
|summarize count() by tostring(split(OuterMessage,'\nClasses\\AGCEventHandlers\\`Global_Pre_error:0\nClasses\\AGCEventHandlers\\Global_Pre_error:0\nClasses\\Global\\`error:0\nClasses\\Global\\error:0\nClasses\\Global\\error:0',0) )
```

