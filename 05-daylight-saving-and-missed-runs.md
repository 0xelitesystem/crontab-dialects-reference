# Daylight saving and missed runs

The schedulers that document daylight saving disagree on a skipped 2:30: GitHub moves it to 3:00, EventBridge Scheduler skips it, and cronie runs it immediately.

## Spring forward and fall back

- GitHub, only when `timezone` is a zone with daylight saving: "scheduled workflows in skipped hours advance to the next valid time. For example, a 2:30 AM schedule advances to 3:00 AM." GitHub does not document what happens to a repeated hour in the fall.
- EventBridge Scheduler: in spring a non-existent time means the invocation "is skipped"; in fall "your schedule runs only once". `rate(1 days)` is a 24-hour duration, not a calendar day. Schedules in UTC get no daylight saving adjustment.
- cronie 1.7.2 describes it two ways, in two man pages. cron(8): "those jobs that would have run in the interval that has been skipped will be run immediately. Conversely, if time was adjusted backward, running the same job twice is avoided." crontab(5): non-existent times "will never match" and repeated times "will cause matching jobs to be run twice".
- Quartz: the tutorial only warns that the shift "can cause a skip or a repeat".
- Kubernetes: the CronJob page does not mention daylight saving.
- Spring: not documented.

## Missed runs

- Kubernetes: "If there are more than 100 missed schedules, then it does not start the Job and logs the error". Setting `startingDeadlineSeconds` narrows the window the controller counts over, and the controller "checks things every 10 seconds".
- GitHub: the schedule event "can be delayed during periods of high loads", high load times "include the start of every hour", and some queued jobs "may be dropped". Scheduled workflows run only on the default branch, and in a public repository they are disabled "when no repository activity has occurred in 60 days".

Sources (checked 2026-10-01):
https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/man/cron.8
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/man/crontab.5
https://www.quartz-scheduler.org/documentation/quartz-2.5.x/tutorials/crontrigger.html
https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
