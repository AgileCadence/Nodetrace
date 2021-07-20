# Configuration

Nodetrace is configured in the _AGCNodetraceParameters_ form \(**/?mi=AGCNodetraceParameters**\). The configuration is applied to all the AOS' connected to the D365 database.

Configuration Fields \(global, not per company\):

* **Send telemetry** - Global switch to enable/disable telemetry
* **Send live metrics** - Enable/Disable Live Metrics for CPU and RAM monitoring in Azure Portal
* **Send performance counters** - Enable/Disable the sending of all performance metrics \(CPU, RAM, etc\) metrics for the Windows OS running the AOS
* **Send long-running batch jobs \(Batch event threshold\)** - Configuration to send alerts when batch jobs run longer than the specified time in minutes
* **Send exceptions** - Global flag for all exception logging \(custom-caught exceptions and those through Global::error\). Recommend this is enabled all the time.
* **Send global errors** - Flag to enable/disable the capture of all invocations of Global::error.
* **Send Ajax** - Injects Javascript to client browser to track form navigation. Due to the nature of the React web application, we suggest only switching on for short periods of time.
* **Send events** - Track custom events as used by developers when relying on the AGCNodetrace APIs. **Needs further documentation**
* **Enable custom event filtering** - All custom events have a name field. This is a global flag to enable/disable custom events before they are sent to Application Insights. Useful to disable chatty or performance-expensive events.

**Custom Event Exclusions Tab** This tab is in effect when the **Enable custom event filtering** field is enabled.

Custom events can be squelched \(prevented from being sent to Azure Application Insights\) if defined in this form. Event name must match the event the user desires to squelch, case insensitive.

Table caching is used so performance impact is on the level of microseconds and does not place any burden on the database.

### Testing the configuration <a id="user-content-testing-configuration"></a>

There is a simple static class that can be used to ensure that the logging is working as-expected.

```text
?mi=SysClassRunner&cls=AGCTestAllMessageTypes
```



