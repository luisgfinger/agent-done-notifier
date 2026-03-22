# Agent Tools

Agent Tools is a small utility repository for agent-assisted terminal workflows.

It was created to solve a practical annoyance: when an agent is working for a while, you often end up checking the terminal over and over just to see whether the task has finished. This repository adds an explicit, opt-in completion sound so you can step away from the terminal and come back only when the work is done.

This project was developed using Codex CLI.

The notification sound is not the Imperial March from Star Wars because that would infringe copyright.

## How `AGENTS.md` Works

The behavior is defined by the instructions in [`AGENTS.md`](./AGENTS.md).

The rules are intentionally simple:

- The notification is only enabled when the user request contains the keyword `afk`.
- `afk` is treated as an explicit opt-in flag.
- If `afk` is not present, the agent must not run the notification script.
- If `afk` is present, the agent must complete the requested work first and then run the notification script before sending the final response.
- The script must run synchronously, which means the final answer should only be sent after the script finishes.
- If the script cannot be executed, the agent should report that failure in the final response.
- The instructions require PowerShell and explicitly tell the agent not to use `Start-Process` for the notification step.
- When the notification is required, the expected command is:

```powershell
& .\agent-tools\notify-done.ps1
```

Example requests:

- `say hello world`
- `say hello world afk`
- `afk say hello world`
- `afk: say hello world`

Only the last three examples should trigger the notification.

## How To Add This To Your Own Project

You can reuse this pattern in your own repository with a fixed layout:

1. Create an `agent-tools` folder at the root of your project.
2. Copy `notify-done.ps1` into that `agent-tools` folder.
3. Create an `AGENTS.md` file at the root of your project, or append these rules to the `AGENTS.md` you already have.

This matters because the completion command used by the instructions is:

```powershell
& .\agent-tools\notify-done.ps1
```

That command assumes the script lives inside an `agent-tools` directory in the project root. If you want to copy the instructions exactly as written, keep that folder structure.

A minimal example looks like this:

````md
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
````

In other words, the expected project structure is:

```text
your-project/
  AGENTS.md
  agent-tools/
    notify-done.ps1
```

## Supported Environment

This repository is currently designed for:

- Windows
- PowerShell
- agent or CLI environments that read and follow repository-level `AGENTS.md` instructions

The notification script uses PowerShell and relies on `[Console]::Beep()` to play a short melody. In environments where console beeps are unavailable, the script handles that case gracefully, but the user may not hear anything.

## Current Limitations

- It is Windows-first. There is no Linux or macOS notification implementation yet.
- It depends on the agent actually respecting `AGENTS.md`. If the agent ignores those instructions, the notification will not run.
- It is synchronous by design, so the agent waits for the sound to finish before sending the final answer.
- Audio support depends on the terminal and execution environment. Some hosts, remote sessions, or restricted consoles may not play the melody.
- The setup assumes the script is stored at `.\agent-tools\notify-done.ps1`, which means adopters need to keep an `agent-tools` folder at the project root if they want to use the instructions as written.

## Repository Purpose

This repository is intentionally small. Its goal is not to be a full notification framework, but to provide a lightweight pattern for agent completion alerts in terminal-based workflows.

The project is also meant to evolve over time, both to work better in its current form and to support other environments beyond the current Windows-first setup. Contributions are welcome, and people should feel free to open improvements, extensions, and adaptations for different workflows or platforms.
