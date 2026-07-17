🧪 Hands-On Lab: Loops

Objective: Practice safe iteration patterns against realistic file and log data.

Tasks:


Create a lab directory /tmp/lab03 containing at least 5 files, with at least one filename containing a space (e.g., touch "/tmp/lab03/my file.txt").
Write a loop using for file in $(ls /tmp/lab03) and observe it mishandle the space-containing filename — record exactly what goes wrong.
Rewrite the same loop using a glob (for file in /tmp/lab03/*) and confirm it now handles every filename correctly, including the one with a space.
Create a small sample log file with at least 10 lines, including some blank lines and lines with leading whitespace, then write a while IFS= read -r line; do ... done < file loop that prints each line prefixed with its line number.
Using find /tmp/lab03 -print0 | while IFS= read -r -d '' file; do ... done, print the size in bytes of every file in the lab directory (hint: stat -c%s "$file" or wc -c < "$file").
Write a script that loops through numbers 1 to 20, printing only the multiples of 3, using continue to skip non-multiples — do not use an if/else, only if + continue.
Write a script that searches a sample log file for a specific pattern and uses break to stop as soon as the first match is found, printing only that first matching line.
Reproduce, in writing only (do not execute anything destructive), a short explanation of how a filename like `touch pwned` could cause unexpected behavior in a naive for file in $(ls) loop, connecting it back to the Cybersecurity Insight section.


Deliverable: All lab scripts, the "what went wrong" notes from Task 2, and the written explanation from Task 8.


Navigation

➡️[ Next: Lesson 04 – Functions](../../docs/Module-04-Bash-scripting/04-functions.md)
