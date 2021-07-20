---
description: Common Scenarios
---

# Extending Nodetrace

### Nodetrace APIs <a id="user-content-nodetrace-apis---common-scenarios"></a>

Nodetrace provides several APIs developers can use to add logging in their own code. This is powerful, as it doesn't suffer the same limitations as infolog and doesn't hit the database at all.

{% hint style="success" %}
Scenario One
{% endhint %}

The first scenario is logging custom events, perhaps in custom X++ code or as a way to log errors that are hard to track down in base code. This is an example of logging when payments fail to process successfully. This can be applied however a developer needs to log to Application Insights.

```java
[ExtensionOf(classStr(MCRCustPaymTotals))]
final class MCRCustPaymTotals_Extension
{

    /// <summary>
    /// Chain of command to log any failures in processing payments
    /// </summary>
    /// <param name = "_paymAction"></param>
    /// <param name = "_allowUserInput"></param>
    /// <param name = "_paymentsCalcAndValid"></param>
    /// <param name = "_custPaymRecID"></param>
    /// <returns></returns>
    public boolean processPayments(MCRProcessPaymAction _paymAction,
                        boolean _allowUserInput,
                        boolean _paymentsCalcAndValid,
                        RecId _custPaymRecID)
    {
        boolean ret = next processPayments(_paymAction,
                            _allowUserInput,
                            _paymentsCalcAndValid,
                            _custPaymRecID);

        // Log errors
        if(!ret)
        {
            // AGCNodetrace is a singleton, so use the Instance method to use
            AGCNodetrace logger = AGCNodetrace::Instance();
            AGCNodetraceEvent evData = new AGCNodetraceEvent();
            evData.parmEventName("MCRCustPaymTotals_processPayments");
            evData.setProperty("Success", "false");
            evData.SetProperty("CallStack", AGCGlobalHelper::formatXppCallStack(xSession::xppCallStack()));
            evData.SetProperty("_paymAction", enum2Symbol(enumNum(MCRProcessPaymAction), _paymAction));
            evData.SetProperty("_allowUserInput", enum2Symbol(enumNum(boolean), _allowUserInput));
            evData.SetProperty("_paymentsCalcAndValid", enum2Symbol(enumNum(boolean), _paymentsCalcAndValid));
            evData.SetProperty("_custPaymRecID", int642Str(_custPaymRecID));
            evData.SetProperty("refTableID", int642Str(this.refTableID));
            evData.SetProperty("refRecID", int642Str(this.refRecID));

            logger.logCustomEvent(evData);
        }

        return ret;
    }

}
```

{% hint style="success" %}
Scenario Two
{% endhint %}

The second scenario is adding performance logging to a retail service, which is pinged by the RCSU when using cPOS/mPOS. This scenario uses the AGCNodetraceScopeFactory which is a wrapper implementing IDisposable so that logging always happens even if exceptions are thrown. IDisposable doesn't suffer the same issues as normal X++ try/catches that have different behaviors depending on the ttslevel.

This is a chain of command method, so it collects the original parameters used in invocation. Behind the scenes, several important pieces of data are collected by default:

1. ActivityId
2. Server Url and AOS name
3. UserId \(not obfusticated\)
4. Start and End times in ISO8601 string format
5. Duration in milliseconds
6. ttslevel
7. Retry count
8. Company
9. Interactive or batch session
10. Session Id

Additional parameters are captured using the AGCNodetraceScopeFactory::SetProperty method. There is also a AGCNodetraceScopeFactory::SetMeasurement method available.

```java
// This is an example method on a class extending the RetailRealTimeServiceContract. The class needs to have this annotation:
//  [ExtensionOf(classStr(RetailRealTimeServiceContract))]
public RetailTransactionServiceResponse InvokeExtensionMethod(RetailTransactionServiceRequestInfo request, str methodName, List parameters)
{
    RetailTransactionServiceResponse resp;

    using(AGCNodetraceScopeFactory scope = new AGCNodetraceScopeFactory("RetailRealTimeServiceContract_InvokeExtensionMethod"))
    {
        // Chain of command method. Have to invoke the base code.
        resp = next InvokeExtensionMethod(request, methodName, parameters);

        // The 'IsLoggingEnabled' method verifies that this custom event is actually being sent to Azure. 
        //  Specifically, that logging is enabled and that this custom event isn't being filtered out.
        // This is not required, but it is recommended if performing expensive lookups (like getting the callstack)
        //  or if there is the potential for logging code to throw an exception.
        if(scope.IsLoggingEnabled())
        {
            scope.SetProperty("methodName", methodName);
            // Example of adding the callstack to the Application Insights event for later analysis.
            str callStack = AGCGlobalHelper::formatXppCallStack(xSession::xppCallStack());
            scope.SetProperty("CallStack", callStack);
        }
    }

    return resp;
}
```

{% hint style="success" %}
Scenario 3
{% endhint %}

Third scenario is logging exceptions caught in X++. Here is an example of logging exceptions for later analysis.

```java
Microsoft.Dynamics.Ax.Xpp.DeadlockException deadlockEx;

try
{
    // Code
}
catch(deadlockEx)
{
    AGCNodetrace cli = AGCNodetrace::Instance();
    // Custom properties/measurements added to this evData variable.
    AGCNodetraceEvent evData = new AGCNodetraceEvent();
    evData.SetProperty("currentRetryCount", any2Str(xSession::currentRetryCount()));
    evData.SetProperty("Class", classStr(CLASSNAME));
    evData.SetProperty("Method", methodStr(CLASSNAME, METHODNAME));
    evData.SetProperty("SalesId", _salesId);
    // Log the exception with the custom event properties/measurements
    cli.logCLRException(deadlockEx, evData);

    if (xSession::currentRetryCount() >= #RetryNum)
    {
        throw Exception::Deadlock;
    }
    retry;
}
```

