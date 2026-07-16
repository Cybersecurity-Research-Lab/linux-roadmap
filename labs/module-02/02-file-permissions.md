🧪 Hands-On Lab: File Permissions

Objective: Practice reading, modifying, and auditing permissions, including special bits.

Tasks:


Create a directory /tmp/lab02 and inside it create three files: public.txt, team.txt, private.txt.
Set public.txt to 644, team.txt to 640, and private.txt to 600.
Create a group lab-team and change team.txt's group ownership to it.
Create a shared directory /tmp/lab02/shared, set its group to lab-team, and apply the SGID bit so new files inherit the group automatically. Verify by creating a new file inside and checking its group with ls -l.
Apply the sticky bit to /tmp/lab02/shared and explain, in one sentence, the practical effect if two different users had write access to that folder.
Find and list every SUID binary on your lab system with the find command from the Cybersecurity Insight. Pick two results and, using man, note what each binary does and why it plausibly needs SUID.
Check your current umask, then create a new file and directory and confirm their default permissions match what the umask predicts.
Deliberately set private.txt to 777, then explain (in writing) why this is a bad practice even on a lab machine, and revert it to 600.


Deliverable: A short write-up (5–8 sentences) summarizing what you found in the SUID audit and one specific real-world risk you'd flag if you found an unexpected binary in that list during a professional assessment.


Navigation

 🏠 Module README | ➡️ [Next: Lesson 03 – Processes](../../docs/Module-02-Working-with-Linux/03-processes.md)
