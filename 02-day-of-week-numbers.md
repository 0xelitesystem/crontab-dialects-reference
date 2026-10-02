# Day-of-week numbers

Sunday is 0 in Linux cron, GitHub Actions, Kubernetes and Spring, and 1 in Quartz and EventBridge, so a numeric weekday range moves by one day when copied between the two camps.

| Scheduler | Documented values |
|---|---|
| POSIX grammar | "[0,6] with 0=Sunday", integers only |
| cronie 1.7.2 | "0-7 (0 or 7 is Sunday, or use names)" |
| GitHub Actions | "0 - 6 or SUN-SAT" |
| Kubernetes CronJob | 0 - 6 (Sunday to Saturday) or sun to sat; robfig v3.0.1 rejects 7 with "end of range (7) above maximum (6): 7" |
| Quartz, EventBridge | "1-7 or SUN-SAT" |
| Spring | "0-6 for SUN-SAT (plus 7 for SUN as well)" |

## The weekday example

GitHub documents `cron: '30 5 * * 1-5'` with `timezone: "America/New_York"` as Monday through Friday. AWS says that in its day-of-week field "the 3 refers to Tuesday because it is the third day of each week". So `cron(30 5 ? * 1-5 *)` in EventBridge Scheduler runs Sunday to Thursday. The faithful version is `cron(30 5 ? * MON-FRI *)` with `--schedule-expression-timezone "America/New_York"`.

## The 6L example

Quartz: "6L" means "the last friday of the month". AWS's `cron(15 10 ? * 6L 2022-2023)` runs on the last Friday. Spring's javadoc gives "0 0 0 * * 5L" as "last Friday of the month", and Spring 7.0.9 maps day numbers through java.time (QuartzCronField.parseDayOfWeek), so `6L` in Spring is the last Saturday. A bare `L` in day-of-week means "7" or "SAT" in Quartz, while Spring throws "No day-of-week before 'L'".

## The fix

Use three-letter names in ranges such as MON-FRI. cronie ("Ranges and lists of names are allowed"), GitHub, Kubernetes, EventBridge, Quartz and Spring all accept names, though the POSIX grammar does not define them.

Sources (checked 2026-10-01):
https://pubs.opengroup.org/onlinepubs/9799919799/utilities/crontab.html
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/man/crontab.5
https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows
https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
https://docs.aws.amazon.com/scheduler/latest/UserGuide/schedule-types.html
https://www.quartz-scheduler.org/documentation/quartz-2.5.x/tutorials/crontrigger.html
https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/support/CronExpression.html
https://github.com/spring-projects/spring-framework/blob/v7.0.9/spring-context/src/main/java/org/springframework/scheduling/support/QuartzCronField.java
