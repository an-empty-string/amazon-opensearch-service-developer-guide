# Auto\-Tune for Amazon OpenSearch Service<a name="auto-tune"></a>

Auto\-Tune in Amazon OpenSearch Service uses performance and usage metrics from your OpenSearch cluster to suggest memory\-related configuration changes, including queue and cache sizes and Java virtual machine \(JVM\) settings on your nodes\. These optional changes improve cluster speed and stability\. 

Some changes deploy immediately, while others require you to schedule a maintenance window\. You can revert to the default OpenSearch Service settings at any time\.

As Auto\-Tune gathers and analyzes performance metrics for your domain, you can view its recommendations in the OpenSearch Service console on the **Notifications** page\.

Auto\-Tune is available in commercial AWS Regions on domains running any OpenSearch version, or Elasticsearch 6\.7 or later, with a [supported instance type](supported-instance-types.md)\.

## Types of changes<a name="auto-tune-types"></a>

Auto\-Tune has two broad categories of changes:
+ Nondisruptive changes that it applies as the cluster runs
+ Changes that require a [blue/green deployment](managedomains-configuration-changes.md)

Based on your domain's performance metrics, Auto\-Tune can suggest adjustments to the following settings:


| Change type | Category | Description | 
| --- | --- | --- | 
|  JVM heap size  |  Blue/green  |  By default, OpenSearch Service uses 50% of an instance's RAM for the JVM heap, up to a heap size of 32 GiB\.  Increasing this percentage gives OpenSearch more memory, but leaves less for the operating system and other processes\. Larger values can decrease the number of garbage collection pauses, but increase the length of those pauses\.  | 
|  JVM young generation settings  |  Blue/green  |  JVM "young generation" settings affect the frequency of minor garbage collections\. More frequent minor collections can decrease the number of major collections and pauses\.  | 
|  Queue size  |  Nondisruptive  |  By default, the search queue size is `1000` and the write queue size is `10000`\. Auto\-Tune automatically scales the search and write queues if additional heap is available to handle requests\.  | 
|  Cache size  |  Nondisruptive  |  The *field cache* monitors on\-heap data structures, so it's important to monitor the cache's use\. Auto\-Tune scales the field data cache size to avoid out of memory and circuit breaker issues\.  The *shard request cache* is managed at the node level and has a default maximum size of 1% of the heap\. Auto\-Tune scales the shard request cache size to accept more search and index requests than what the configured cluster can handle\.  | 
| Request size | Nondisruptive |  By default, when the aggregated size of in\-flight requests surpasses 10% of total JVM \(2% for `t2` instance types and 1% for `t3.small`\), OpenSearch throttles all new `_search` and `_bulk` requests until the existing requests complete\.  Auto\-Tune automatically tunes this threshold, typically between 5\-15%, based on the amount of JVM that is currently occupied on the system\. For example, if JVM memory pressure is high, Auto\-Tune might reduce the threshold to 5%, at which point you might see more rejections until the cluster stabilizes and the threshold increases\.  | 

If you enable Auto\-Tune without setting a maintenance window, Auto\-Tune only applies nondisruptive changes\. The performance benefits over time are generally smaller, but you avoid the overhead associated with blue/green deployments\.

For guidance on configuring maintenance windows, see [Scheduling changes](#auto-tune-schedule)\.

## Enabling or disabling Auto\-Tune<a name="auto-tune-enable"></a>

OpenSearch Service enables Auto\-Tune by default on new domains\. To enable or disable Auto\-Tune on existing domains, we recommend using the console, which greatly simplifies the process\. In the console, choose your domain and go to the **Auto\-Tune** tab, then choose **Edit**\. Enabling Auto\-Tune doesn't cause a blue/green deployment\.

**AWS CLI**

To use the [AWS CLI](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/opensearch/index.html), configure the `auto-tune-options` parameters\. The following sample command enables Auto\-Tune on an existing domain with a maintenance schedule that repeats every day at 12:00pm UTC:

```
aws opensearch update-domain-config \
  --domain-name mylogs \
  --auto-tune-options '{"DesiredState": "ENABLED","MaintenanceSchedules":[{"StartAt":"2021-12-19","Duration":{"Value":2,"Unit":"HOURS"},"CronExpressionForRecurrence": "cron(0 12 * * ? *)"}]}'
```

**Configuration API**

To use the [configuration API](https://docs.aws.amazon.com/opensearch-service/latest/APIReference/API_UpdateDomainConfig.html), configure the `AutoTuneOptions` settings: 

```
POST https://es.us-east-1.amazonaws.com/2021-01-01/opensearch/domain/domain-name/config
{
  "AutoTuneOptions": {
    "DesiredState": "ENABLED",
    "MaintenanceSchedules": [{
      "StartAt": 4104152288000,
      "Duration": {
        "Value": 2,
        "Unit": "HOURS"
      },
    "CronExpressionForRecurrence": "cron(0 12 * * ? *)"
    }]
  }
}
```

**CloudFormation**

You currently can't enable or disable Auto\-Tune using AWS CloudFormation\.

## Scheduling changes<a name="auto-tune-schedule"></a>

To apply changes that require a blue/green deployment, you schedule a maintenance window for your domain—for example, between 6:00 and 9:00 AM on a Friday morning\. We recommend scheduling maintenance windows for low\-traffic times\.
+ To review all changes before deploying them, wait for Auto\-Tune to notify you of a suggested optimization\. Then schedule a one\-time maintenance window to deploy the changes\.
+ For a more automated experience, set a weekly maintenance window, such as every Saturday at 2:00 AM, or use a custom [cron expression](#auto-tune-cron) for more complex schedules\.

To schedule changes in the console, choose your domain, go to the **Auto\-Tune** tab, choose **Edit**, and then select **Add maintenance window**\. This tab also shows your current maintenance window and whether Auto\-Tune will make any changes during the next window\.

## Cron expressions<a name="auto-tune-cron"></a>

Cron expressions for Auto\-Tune use the same six\-field syntax as [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions):

```
minute hour day-of-month month day-of-week year
```

For example, the following expression translates to "every Tuesday and Friday at 1:15 AM from 2021 through 2024":

```
15 1 ? * 2,5 2021-2024
```

The following table includes valid values for each field\.


| Field | Valid values | 
| --- | --- | 
|  Minute  |  0–59  | 
|  Hour  |  0–23  | 
|  Day of month  |  1–31  | 
|  Month  |  1–12 or JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP, OCT, NOV, DEC  | 
|  Day of week  |  1–7 or SUN, MON, TUE, WED, THU, FRI, SAT  | 
|  Year  |  1970–2199  | 

Day of month and day of week overlap, so you can specify one, but not both\. You must mark the other as `?`\. For a full summary of wildcard options, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions)\.