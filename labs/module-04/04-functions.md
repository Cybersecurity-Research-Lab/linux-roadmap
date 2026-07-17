🧪 Hands-On Lab: Functions

Objective: Practice function structure, scoping discipline, and both return patterns.

Tasks:


Write a function is_number that takes one argument and returns exit status 0 if it's a valid integer (use a regex or pattern match) and 1 otherwise; test it against several valid and invalid inputs using if is_number "$val"; then ... fi.
Write a function count_lines that takes a filename as an argument and echoes the number of lines in it (using wc -l internally), and capture its result into a variable using command substitution.
Write a function log_message that takes a message as an argument and prints it prefixed with the current timestamp (e.g., [2026-07-17 10:30:00] message), and call it several times from a small script to confirm consistent formatting.
Deliberately write a function that assigns to a global-sounding variable name (e.g., status) WITHOUT local, call it from a script that also uses a variable named status for something else beforehand, and demonstrate the bug where the function silently overwrites it. Then fix it by adding local and confirm the bug is resolved.
Write a function validate_and_report that combines is_number and log_message from above: it should take a value, check if it's a valid number using is_number, and log an appropriate success or failure message using log_message — demonstrating functions calling other functions.
Refactor one of your Lesson 03 loop-based scripts (e.g., the log file scanner) to call a new function for the per-file logic instead of putting it directly in the loop body, and confirm behavior is unchanged.


Deliverable: All function definitions and test scripts from this lab, plus 2–3 sentences describing exactly what went wrong in Task 4 before the local fix, and why that failure mode is more dangerous in a longer, more complex script.


Navigation

 ➡️[ Next: Lesson 05 – Bash Automation](../../docs/Module-04-Bash-scripting/05-bash-automation.md)
