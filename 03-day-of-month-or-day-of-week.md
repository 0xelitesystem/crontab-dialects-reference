# Day of month OR day of week

When both day fields are restricted, Linux cron and Kubernetes run on either match, Quartz and EventBridge forbid restricting both, and "restricted" itself is defined differently.

## The OR rule

POSIX Issue 8: "if either the month or day of month is specified as an element or list, and the day of week is also specified as an element or list, then any day matching either the month and day of month, or the day of week, shall be matched." cronie's crontab(5) gives "30 4 1,15 * 5" as 4:30 am "on the 1st and 15th of each month, plus every Friday".

## Where the OR camp splits

cronie 1.7.2 calls a day field unrestricted when its first character is '*'. The man page says "(i.e., do not contain the "*" character)", entry.c line 251 sets `if (ch == '*') e->flags |= DOM_STAR;`, and cron.c line 582 ANDs the day fields when either star flag is set. robfig/cron v3.0.1, which Kubernetes uses, drops the star flag when a step above 1 follows it (parser.go getRange: `if step > 1 { extra = 0 }`), and spec.go dayMatches ORs unless a star bit is set.

Take `0 0 */2 * 1` in September 2026, UTC (1 September is a Tuesday):

- cronie: Mondays on odd dates, so 7 and 21.
- Kubernetes (robfig v3.0.1, run with Go 1.27.1): 1, 3, 5, 7, 9, 11, 13, 14, 15, 17, 19, 21, 23, 25, 27, 28, 29.
- Kubernetes with `*/1`: the star survives, so Mondays only (7, 14, 21, 28).

## No OR at all

AWS: "You can't specify the Day-of-month and Day-of-week fields in the same cron expression. If you specify a value or a * (asterisk) in one of the fields, you must use a ? (question mark) in the other." Quartz: support for both "is not complete (you must currently use the '?' character in one of these fields)". In Kubernetes "?" "has the same meaning as an asterisk", and Spring allows "?" in place of "*" in either day field.

Sources (checked 2026-10-01):
https://pubs.opengroup.org/onlinepubs/9799919799/utilities/crontab.html
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/man/crontab.5
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/src/entry.c
https://github.com/cronie-crond/cronie/blob/cronie-1.7.2/src/cron.c
https://github.com/robfig/cron/blob/v3.0.1/parser.go
https://github.com/robfig/cron/blob/v3.0.1/spec.go
https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-scheduled-rule-pattern.html
https://www.quartz-scheduler.org/documentation/quartz-2.5.x/tutorials/crontrigger.html
https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
