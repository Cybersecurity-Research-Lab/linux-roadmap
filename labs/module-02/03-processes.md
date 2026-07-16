🧪 Hands-On Lab: Processes

Objective: Practice process inspection, control, and basic anomaly recognition.

Tasks:


Start three background jobs: sleep 500 &, sleep 600 &, and yes > /dev/null &. List them with jobs.
Use ps aux to find the PIDs of all three, and confirm they match what jobs reported.
Use top (or htop if installed) to identify which of the three is consuming the most CPU. Record your observation.
Gracefully terminate the yes process using SIGTERM, then confirm it's gone from ps aux.
Force-kill one of the remaining sleep jobs with SIGKILL and explain, in one sentence, why SIGTERM would have worked equally well in this specific case (hint: think about what sleep is actually doing).
Run pstree -p and identify your current shell's PID and its parent.
Investigate one running process of your choice using /proc/<PID>/exe, /proc/<PID>/cmdline, and lsof -p <PID>. Write two sentences summarizing what you learned about it.
Simulate the "suspicious child process" pattern safely: from your shell, run sh -c 'sleep 60' in the background, then use ps auxf to observe the parent/child relationship it creates, and explain how this maps to the nginx→sh→nc example from the lesson.


Deliverable: A short log of commands and outputs, plus a 3–4 sentence write-up describing what a normal process tree looks like on your lab system versus what you'd flag as suspicious.

Navigation

 ➡️ [Next: Lesson 04 – Services](../../docs/Module-02-Working-with-Linux/04-services.md)
