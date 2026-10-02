# Fields and order

Linux cron, GitHub Actions and Kubernetes read five fields, EventBridge reads six with the year last, and Spring reads six and Quartz six or seven with seconds first.

| Scheduler | Fields, in order |
|---|---|
| Linux cron (POSIX grammar, cronie 1.7.2) | minute, hour, day of month, month, day of week |
| GitHub Actions | the same five |
| Kubernetes CronJob | the same five |
| EventBridge rules and Scheduler | minutes, hours, day-of-month, month, day-of-week, year (1970-2199) |
| Quartz | seconds, minutes, hours, day of month, month, day of week, optional year (1970-2099) |
| Spring CronExpression | second, minute, hour, day of month, month, day of week |

POSIX Issue 8: "The first five fields shall be integer patterns". GitHub: "Cron syntax has five fields". robfig/cron v3.0.1, the parser Kubernetes pins, rejects six with "expected exactly 5 fields, found 6". AWS: "six required fields". Spring throws "Cron expression must consist of 6 fields".

## The six-field trap

A six-field Spring or Quartz string and a six-field EventBridge string share a count, not a layout. The first field is seconds in one and minutes in the other. Count the fields, then read the first one.

## Macros

Kubernetes lists @yearly (@annually), @monthly, @weekly, @daily (@midnight) and @hourly, and the Spring javadoc lists the same set. GitHub "does not support the non-standard syntax @yearly, @monthly, @weekly, @daily, @hourly, and @reboot". The POSIX grammar defines none.

## Steps

GitHub says "Use POSIX cron syntax" and also documents the / step operator. The POSIX grammar defines an asterisk, a number, a range and a list, so step support comes from implementations, not the standard.

Sources (checked 2026-10-01):
https://pubs.opengroup.org/onlinepubs/9799919799/utilities/crontab.html
https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
https://github.com/robfig/cron/blob/v3.0.1/parser.go
https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html
https://www.quartz-scheduler.org/documentation/quartz-2.5.x/tutorials/crontrigger.html
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/support/CronExpression.html
