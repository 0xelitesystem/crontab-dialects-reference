# Intervals and a translation checklist

The finest interval runs from one second (Quartz, Spring) to one minute (Linux cron, Kubernetes, EventBridge) to five minutes (GitHub).

## Minimum intervals

- Quartz and Spring: the first field is seconds, so a schedule can fire every second.
- cronie: "cron(8) examines cron entries every minute."
- Kubernetes: five fields, so the minute is the smallest unit.
- EventBridge: "Cron expressions that lead to rates faster than 1 minute are not supported", and Scheduler invokes targets "with 60 second precision".
- GitHub: "The shortest interval you can run scheduled workflows is once every 5 minutes."

## Ranges that wrap past midnight

AWS's own example `0/30 20-2 ? * MON-FRI *` runs every 30 minutes from 10:00 pm to 2:00 am the next day. The Quartz CronExpression javadoc says "Overflowing ranges is supported", for example 22-2. cronie's crontab(5) says "The first number must be less than or equal to the second one." robfig/cron v3.0.1, used by Kubernetes, rejects `0 20-2 * * *` with "beginning of range (20) beyond end of range (2): 20-2". Split a wrapping range into a list, such as `20-23,0-2`, when it has to travel.

## Translation checklist

1. Count the fields and name the first and last one.
2. Write weekdays as names (MON-FRI), never as numbers.
3. Put `?` in one day field where Quartz or EventBridge require it.
4. Check whether a `*/n` day field turns OR into AND, or back.
5. Set the time zone explicitly; never trust the default.
6. Read the target's daylight saving rule for times near a clock change.
7. Check the target's minimum interval.
8. Check that wrapping ranges are accepted, or split them.

Sources (checked 2026-10-01):
https://www.quartz-scheduler.org/api/2.3.0/org/quartz/CronExpression.html
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/man/crontab.5
https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-scheduled-rule-pattern.html
https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html
https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
https://github.com/robfig/cron/blob/v3.0.1/parser.go
