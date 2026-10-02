# Time zones

Only EventBridge scheduled rules are fixed to UTC with no option; every other scheduler here defaults to UTC or to some machine's local zone, and lets you override it per schedule.

| Scheduler | Default | Override |
|---|---|---|
| Linux cron (cronie 1.7.2) | the daemon's local zone | CRON_TZ in the crontab |
| GitHub Actions | UTC | `timezone` (IANA name) |
| Kubernetes CronJob | kube-controller-manager's local zone | `.spec.timeZone` |
| EventBridge scheduled rules | UTC+0 | none |
| EventBridge Scheduler | UTC | `--schedule-expression-timezone` |
| Quartz | JVM default | CronScheduleBuilder.inTimeZone |
| Spring @Scheduled | the scheduler's zone | `zone` attribute |

## Details

- cronie: "The CRON_TZ variable specifies the time zone specific for the cron table."
- GitHub: "By default, scheduled workflows run in UTC." The `timezone` key was announced on 2026-03-19 and is gated to GitHub.com, GHEC and GHES 3.22 or later; the GHES 3.21 page lacks it.
- Kubernetes: "For CronJobs with no time zone specified, the kube-controller-manager interprets schedules relative to its local time zone." `.spec.timeZone` is stable since v1.27. A new CronJob with TZ or CRON_TZ in its schedule fails with "cannot use TZ or CRON_TZ in schedule, use timeZone field instead", and "Local" is rejected.
- EventBridge rules: "All scheduled events use UTC+0 time zone". AWS calls scheduled rules "a legacy feature".
- Quartz: if unset, getTimeZone() returns TimeZone.getDefault().
- Spring: `zone` "is the empty String (i.e. the scheduler's time zone will be used)".

Sources (checked 2026-10-01):
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/man/crontab.5
https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
https://github.blog/changelog/2026-03-19-github-actions-late-march-2026-updates/
https://github.com/github/docs/blob/2a23299/data/features/actions-schedule-timezone.yml
https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
https://github.com/kubernetes/kubernetes/blob/v1.37.1/pkg/apis/batch/validation/validation.go
https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html
https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html
https://github.com/quartz-scheduler/quartz/blob/v2.5.2/quartz/src/main/java/org/quartz/CronExpression.java
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/Scheduled.html
