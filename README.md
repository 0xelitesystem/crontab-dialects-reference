# Cron Dialects Reference

The same cron string fires on different days in Linux cron, Quartz, Spring, AWS EventBridge, GitHub Actions and Kubernetes. A side by side reference to field counts, day-of-week numbers, the day-of-month OR day-of-week rule, time zones, daylight saving and minimum intervals, each cited to the scheduler's own docs or source.

Every chapter quotes the scheduler's own documentation or a pinned release of its source, and ends with the URLs and the date they were checked. Where a scheduler documents nothing, the chapter says so instead of guessing.

## The core idea

A cron expression is not portable: six common schedulers parse the same characters with different field orders, day numbers and day-matching rules, so a string that is correct in one can be valid and wrong in another. GitHub documents `30 5 * * 1-5` as Monday through Friday. AWS numbers Sunday as 1, so the same `1-5` in an EventBridge expression runs Sunday through Thursday. Both are right by their own published rules.

## What is inside

- [01-fields-and-order.md](01-fields-and-order.md) five, six or seven fields, and whether seconds come first or the year comes last.
- [02-day-of-week-numbers.md](02-day-of-week-numbers.md) where Sunday is 0 and where it is 1, plus why `6L` means a different day in Spring.
- [03-day-of-month-or-day-of-week.md](03-day-of-month-or-day-of-week.md) the OR rule, and how `*/2` gives 2 runs in Linux cron but 17 in Kubernetes in September 2026.
- [04-time-zones.md](04-time-zones.md) each scheduler's default zone and how to override it.
- [05-daylight-saving-and-missed-runs.md](05-daylight-saving-and-missed-runs.md) what a skipped or repeated hour does, and when missed runs are dropped.
- [06-intervals-and-translation-checklist.md](06-intervals-and-translation-checklist.md) minimum intervals, ranges that wrap past midnight, and an eight-step checklist.

## How to use it

- **Moving a job between platforms.** Porting a GitHub Actions schedule to EventBridge Scheduler or a Kubernetes CronJob: run the checklist in chapter 06 line by line before you deploy.
- **Reviewing a pull request that adds a schedule.** Check the weekday field against chapter 02 and any `*/n` day field against chapter 03.
- **Debugging a job that ran on the wrong day or hour.** Chapter 03 covers the wrong day, chapters 04 and 05 the wrong hour.
- **Writing a schedule for a team that uses several schedulers.** Use weekday names, set the time zone explicitly, and record which dialect the string is written for.

## The stance

Each scheduler is right by its own published rules. The breakage happens when a string is copied from one to another. Translate by meaning, not by copying characters: count the fields, write weekdays as names such as MON-FRI, and set the time zone on purpose.

This is an independent project. It is not affiliated with, endorsed by or sponsored by Amazon Web Services, GitHub, the Kubernetes project, Quartz, Spring or cronie. Product names are used only to identify the software being described.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

Related: [cron-builder](https://github.com/0xelitesystem/cron-builder), [cron-expression-explainer](https://github.com/0xelitesystem/cron-expression-explainer), [github-actions-expression-evaluator](https://github.com/0xelitesystem/github-actions-expression-evaluator), [timezone-and-utc-basics-reference](https://github.com/0xelitesystem/timezone-and-utc-basics-reference), [background-jobs-and-queues-basics-reference](https://github.com/0xelitesystem/background-jobs-and-queues-basics-reference).

## License

MIT. Copyright 0xelitesystem 2026.
