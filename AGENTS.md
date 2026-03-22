# AGENTS.md

# Agent Instructions

## Completion behavior

This rule is conditional and applies only when the current user request includes the keyword `afk`.
Treat `afk` as an explicit opt-in flag for the completion notification script.
If the current user request does not include `afk`, do not execute the notification script before sending the final response.
If the current user request includes `afk`, execute the notification script SYNCHRONOUSLY before sending any final response after completing the requested work.
When `afk` is present, the task is not complete until the script execution has finished.
If the script cannot be executed when `afk` is present, explicitly report that failure in the final response.

Use PowerShell in the current session so the sound plays reliably.
Do not use `Start-Process` for this notification.
Wait for it to finish before sending the final answer when `afk` is present.
Do not replace this with another notification mechanism.
Do not send the final answer first and execute it later when `afk` is present.
When `afk` is present, always use this command:

```powershell
& .\agent-tools\notify-done.ps1
```

Examples:

- `say hello world` -> do not execute the script.
- `say hello world afk` -> execute the script before the final response.
- `afk say hello world` -> execute the script before the final response.
- `afk: say hello world` -> execute the script before the final response.