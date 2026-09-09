# Recovering a Minecraft Java World After an Unclean Shutdown

## Summary

Before building my current Proxmox-based homelab, I ran a small Minecraft Java server on a personal PC. The server was not cleanly stopped before the system lost power, and the world became corrupted. I recovered the server by replacing the damaged world directory with a recent backup.

This was an earlier standalone-PC recovery, not a restore test of my current homelab backup platform. It taught me to treat backups as a recovery process that must be validated, not simply as files that exist.

## Incident

The Minecraft Java server was still running when I exited rather than using a clean save-and-stop procedure. A subsequent power loss left the active world in a corrupted state.

My immediate objective was to restore a known-good world copy, prevent additional writes to the damaged data, and return the server to a usable state with minimal loss of recent progress.

## Recovery Process

1. I stopped the Minecraft Java server to prevent further writes to the corrupted world data.
2. I identified a recent backup containing a known-good copy of the world folder.
3. I worked from the backup rather than attempting to continue using the corrupted world data.
4. I replaced the active world folder with the copied backup.
5. I started the server and confirmed that the recovered world loaded successfully.
6. I verified that expected world data and builds were present after the restore.

## Outcome

The restored world loaded successfully and was usable again. The recovery caused minimal data loss—approximately five minutes of recent progress since the last successful backup.

## Lessons Learned

- Stateful services should be stopped cleanly before shutting down the host system.
- A backup is only useful if it can be restored and validated.
- Keeping recent backup copies provides a practical rollback point after corruption or an unplanned shutdown.
- Recovery steps should prioritize stopping additional writes before replacing damaged application data.
- I now verify that backups complete successfully instead of assuming a scheduled or manual backup has run.

## How This Influences My Current Homelab

This experience influenced how I design and operate my current homelab. I use documented backup procedures, retention planning, encrypted off-host storage, and recovery runbooks. I also treat restore validation as a separate operational task from backup creation.
