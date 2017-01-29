> Schedule a reoccurring background job rendering specific report template

##Basics
Every report rendering time plan is specified by the report template and [CRON expression](http://crontab.org/) identifying time occurrence. To create a rendering time plan you need to create and enable `Schedule` entity from the jsreport studio or API. When a `Schedule` is enabled you can veriffy `NextRun` property and every time rendering is done you can also download output report from the studio or API entity `Task`.

`Scheduling` requires and heavily relies on [scripts](/learn/scripts) and [reports](/learn/reports) extension. Where reports are used to store rendering output and scripts are usually used to fetch input data and send the result.

Common use case for `scheduling` extension can be daily sending summary report. Where`schedule` report template will define document with summary tables and charts. Script will fetch input data before rendering and afterwards sends report by mail or upload result to external service.

##CRON expression

CRON expression is unix standard for specifying time occurrence.  It is a string which consists from 6 segments. 

1. Seconds: 0-59
2. Minutes: 0-59
3. Hours: 0-23
4. Day of Month: 1-31
5. Months: 0-11
6. Day of Week: 0-6

Every segment identifies particular unit. In every segment you can use also wildcard `*`,  interval (1-5) and steps (*/5).

Some examples:

- `*/10 * * * * *` - run every 10 seconds
- `00 30 11 * * 1-5` - run every weekday at 11:30:00

You can find full document with specification [here](http://crontab.org/).

##Configuration
Add `scheduling` node to the standard [config](https://github.com/jsreport/jsreport/blob/master/config.md) file. The defaults are following.

```js
scheduling: {
  //how often in ms jsreport checks scheduled jobs
  interval: 5000,
  //how many jobs can run in parallel
  maxParallelJobs: 5    
}
```

##Quick links
> **[Sending reports periodically in email article](https://jsreport.net/blog/sending-reports-periodically-in-email)**