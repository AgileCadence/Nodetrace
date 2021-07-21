# Form Performance

{% hint style="info" %}
When a system user requests and form within D365 it will be logged to Application Insights. Spotting trends in the server render time helps guide to performance hotspots.
{% endhint %}

```sql
AppPageViews
| summarize RequestsCount=sum(ItemCount), AverageDuration=avg(DurationMs), percentiles(DurationMs, 50, 95, 99) by Name
| order by AverageDuration desc 
```

Page request trend:

```sql
AppPageViews
| summarize count_sum = sum(ItemCount) by bin(TimeGenerated,30m), _ResourceId
| render timechart
```

Users most effected by slow page loads:

```sql
AppPageViews
| where Name == "ReturnTable"
| summarize count() by UserId
| order by count_ desc 
```

Show the activity ids of the most effected user and form:

```sql
AppPageViews
| where Name == "<--Form name here-->" and UserId == "<--User id here-->"
| summarize count() by UserId, tostring(Properties.ActivityId), DurationMs
| order by DurationMs
```

What happened during the poorly performing activity id?:

```sql
AppPageViews
| where tostring(Properties.ActivityId) == "<--Activity id here-->"
```

