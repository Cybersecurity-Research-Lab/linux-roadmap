🧪 Hands-On Lab: Environment Variables

Objective: Practice variable scope, persistence, and recognize PATH-based risk.

Tasks:


Print your full environment with printenv and identify the values of PATH, HOME, SHELL, and USER.
Set a shell-local (non-exported) variable called LABTEST with some value, then confirm a subshell (bash -c 'echo $LABTEST') does not see it.
Export LABTEST and confirm the subshell now sees it.
Add a persistent variable named LAB_ENV=training to your ~/.bashrc, then open a fresh shell (or source the file) and confirm it's set automatically.
Reproduce the PATH-hijacking demonstration from the Quick Practice section in a disposable lab directory, and write 3–4 sentences explaining, step by step, why the fake ls executed instead of the real one.
Clean up: remove the fake ls script, remove the test directory from PATH (open a new shell if needed), and remove the LAB_ENV line you added to .bashrc.
Use env -i bash -c 'echo PATH is: $PATH' and observe what a completely clean environment's PATH looks like (often empty or minimal) — explain why this matters for testing scripts.


Deliverable: A short log of your commands and their output, plus the 3–4 sentence explanation from step 5.


Navigation

➡️ [Next: Lesson 07 – Shell Redirection & Pipes](../../docs/Module-02-Working-with-Linux/07-shell-redirection-pipes.md)
