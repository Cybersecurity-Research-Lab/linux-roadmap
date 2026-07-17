🧪 Hands-On Lab: Cron Jobs

Objective: Schedule real automation from earlier lessons, deliberately reproduce the classic cron environment bug, and practice a cron security audit.

Tasks:


Take the backup_configs.sh script from Lesson 05's lab and schedule it to run every 5 minutes using crontab -e, redirecting its output to a log file.
Wait for at least one scheduled execution and confirm, via the log file, that it ran successfully and on the expected schedule.
Deliberately introduce a PATH-dependent bug: modify a copy of the script to call a command by name only (no absolute path) that only works because of your interactive shell's PATH/profile setup, schedule that broken version, and observe it fail under cron even though it works fine when run manually. Record the exact failure.
Fix the bug using absolute paths, reschedule, and confirm it now works correctly under cron too.
Remove the cron entry (edit it out with crontab -e, not crontab -r, to practice the safer targeted removal).
Perform a full cron audit on your lab system: check your own crontab, /etc/crontab, /etc/cron.d/, and all four cron.* directories, listing everything you find.
Add a clearly-labeled, harmless test entry to /etc/cron.d/ (following correct syntax including the user field) that just logs a timestamp, confirm it runs, then remove it and confirm your audit process from Task 6 would have caught it while it existed.


Deliverable: Your final crontab entry, the failure log from the deliberate PATH bug in Task 3, and the full audit results from Task 6, plus 2–3 sentences on why checking /etc/cron.d/ specifically matters beyond just your own personal crontab.


Navigation

 ➡️ [Troubleshooting Guide](../../Troubleshooting/Module-04.md)
