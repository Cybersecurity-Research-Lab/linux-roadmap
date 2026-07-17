🧪 Hands-On Lab: Bash Fundamentals

Objective: Write, execute, debug, and safely inspect basic Bash scripts.

Tasks:


Write a script called system_info.sh that prints the current user, hostname, current date, and current working directory, each on its own line, using command substitution ($(...)).
Make it executable and run it using ./system_info.sh.
Run the same script a second way, using bash system_info.sh without relying on the execute bit, and confirm the output is identical.
Intentionally introduce a syntax error (e.g., an unclosed quote) and run bash -n system_info.sh to catch it without executing the script — record the error message.
Fix the error, then add set -euo pipefail near the top of the script, followed by a deliberately failing command (e.g., ls /nonexistent_directory) after your existing lines — run it and confirm the script stops immediately at that point rather than continuing.
Remove the deliberately failing command, run the script one final time, and check echo $? immediately afterward to confirm a 0 exit code.
Write a second, tiny script that only contains curl http://example.com/install.sh | bash (do not actually connect anywhere real — this is for static analysis practice only, feel free to use a placeholder domain). Practice the "safe first look" workflow: read the file with cat, run bash -n on it, and write 2–3 sentences describing what red flags this pattern would represent if you found it on a system you were investigating.


Deliverable: The final system_info.sh script content, the syntax error message from Task 4, and your 2–3 sentence write-up from Task 7.


Navigation

 ➡️ [Next: Lesson 02 – Variables](../../docs/Module-04-Bash-scripting/02-variables.md)
