🧪 Hands-On Lab: Services

Objective: Create, manage, and audit a custom systemd service end to end.

Tasks:


Write a tiny script at /usr/local/bin/heartbeat.sh that appends a timestamp to /tmp/heartbeat.log every 10 seconds in an infinite loop (a simple while true; do echo "$(date)" >> /tmp/heartbeat.log; sleep 10; done works fine).
Make the script executable.
Create a systemd unit file /etc/systemd/system/heartbeat.service that runs this script as a dedicated non-root user (create one if needed, e.g., heartbeatuser, following Lesson 01's account creation steps), with Restart=always.
Reload systemd, then enable and start the service.
Confirm it's running with systemctl status heartbeat and by tailing /tmp/heartbeat.log.
Use journalctl -u heartbeat -f to watch its log output live for 20–30 seconds.
Kill the underlying process directly with kill -9 (find its PID via systemctl status heartbeat), then re-check systemctl status a few seconds later — record what happened and explain why, referencing Restart=always.
Disable and stop the service, then remove the unit file and reload systemd again to fully clean up.
Run systemctl --failed and systemctl list-unit-files --state=enabled on your system and note anything unfamiliar — treat this as a mini real-world audit.


Deliverable: The full content of your heartbeat.service unit file, plus 3–4 sentences describing what happened after you killed the process and why that mechanism is attractive to attackers as well as legitimate administrators.


Navigation

➡️ [Next: Lesson 05 – Package Managers](../../docs/Module-02-Working-with-Linux/05-package-managers.md)
