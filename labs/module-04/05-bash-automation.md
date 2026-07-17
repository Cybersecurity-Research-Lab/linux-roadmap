🧪 Hands-On Lab: Bash Automation

Objective: Build a complete, realistic automation script incorporating logging, error handling, and safe concurrency.

Tasks:


Write a script backup_configs.sh that copies every .conf file from a lab source directory (create one with a few sample .conf files) into a timestamped backup directory (e.g., /tmp/backups/2026-07-17_143000/), using set -euo pipefail, absolute paths, and a log() function that writes to both the terminal and a log file.
Add a lock file check with trap ... EXIT cleanup to prevent the script from running twice simultaneously; test this by running it in the background and immediately trying to run it again.
Add a guard for the case where the source directory contains no .conf files at all (glob matches nothing) and confirm the script handles this gracefully rather than trying to "back up" a literal *.conf string.
Add a --dry-run flag: when passed, the script should log what it would copy without actually copying anything. Test both modes and confirm the dry run makes no filesystem changes.
Deliberately break something the script depends on (e.g., make the source directory unreadable, or reference a missing prerequisite command) and confirm the script fails loudly and logs a clear error message, rather than failing silently or producing a confusing partial result.
Review your finished script specifically for the properties mentioned in the Researcher's Note: absolute paths for every command, no reliance on PATH, appropriate file permissions if this were to run privileged, and basic input validation if it accepts arguments.


Deliverable: The final backup_configs.sh script, its log output from both a successful run and a deliberately-broken run, and 2–3 sentences explaining how you addressed each property from Task 6.


Navigation

 ➡️[ Next: Lesson 06 – Cron Job](../../docs/Module-04-Bash-scripting/06-vron-jobs.md)
