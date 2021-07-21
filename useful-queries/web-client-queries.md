# Web Client

What browsers are being used:

```sql
AppDependencies
| summarize count() by ClientBrowser
| render barchart 
```

{% hint style="warning" %}
 Make sure your system users are using a Chromium based browser
{% endhint %}

What locations are my users connecting from:

```sql
AppDependencies
| summarize count() by ClientCity, ClientCountryOrRegion
| render barchart 
```

How is my system today on key tasks:

```sql
AppDependencies
| where Name contains "navigationsearch"
| summarize totalCount=sum(DurationMs) by bin(TimeGenerated, 30m)
| render timechart
```

