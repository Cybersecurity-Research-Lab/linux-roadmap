🧪 Hands-On Lab: Variables

Objective: Practice variable declaration, quoting discipline, and argument handling.

Tasks:


Write a script greet.sh that accepts a name as its first argument and prints Hello, <name>! — use ${1:-World} so it defaults gracefully to "World" if no argument is given.
Create a test file with a space in its name (e.g., touch "test file.txt"), then write a small script snippet that attempts to check for its existence using an unquoted variable, and observe it fail or behave unexpectedly; then fix it with proper quoting and confirm it works correctly.
Write a script that accepts any number of arguments and prints each one on its own line using a loop over "$@" (a light preview of Lesson 03), then repeat using "$*" instead and explain the difference in output when you pass an argument containing a space.
Create an array of at least 4 file extensions (e.g., .log, .txt, .conf, .sh), print the total count using ${#array[@]}, and print each element on its own line.
Run shellcheck against every script you wrote in this lab (install it if not already present) and fix any warnings it raises, documenting what each warning meant.
Write a short, clearly-labeled demonstration script showing the dangerous pattern of building a command string with unquoted, unsanitized "input" (use a hardcoded example value containing a semicolon, like input="harmless; echo INJECTED") and passing it to eval, and observe the injected command actually execute. Do this only in your disposable lab environment, and immediately follow it with a corrected version that avoids eval and demonstrates safe handling instead.


Deliverable: All script files from this lab, the shellcheck output before and after your fixes, and 2–3 sentences describing what you observed in Task 6 and why it matters for any script that processes external input.


Navigation
➡️ [Next: Lesson 03 – Loops](../../docs/Module-04-Bash-scripting/03-loops.md)
