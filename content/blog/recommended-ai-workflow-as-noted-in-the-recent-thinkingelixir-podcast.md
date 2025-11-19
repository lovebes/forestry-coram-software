+++
date = 2025-11-18T08:00:00Z
description = "AI workflow recommendations discussed in the ThinkingElixir podcast"
image = "https://elixir-lang.org/images/logo/logo.png"
title = "Recommended AI Workflow as noted in the recent ThinkingElixir podcast"

+++


## Getting Started: Installing Claude Code in VSCode

1. **Open Your Project in VSCode**  
   Make sure your project folder is open in Visual Studio Code before proceeding.

2. **Open the Integrated Terminal**  
   Use `Ctrl + backtick` or select `Terminal → New Terminal` from the menu.

3. **Install or Launch Claude Code**  
   - Run `claude` in the terminal. The extension often auto-installs or prompts you to finalize setup.  
   - To hook Claude Code to the IDE terminal context, run `/ide` after launching Claude.

4. **Check/Install Code Command in PATH (if needed)**  
   Use the Command Palette (`Cmd+Shift+P` or `Ctrl+Shift+P`) and select:  
   `"Shell Command: Install 'code' command in PATH"`.

## Core Features in VSCode

- **Claude Panel Activation:**  
  Use `Cmd+Esc` (Mac) or `Ctrl+Esc` (Windows/Linux) to open Claude chat inside VSCode.

- **Interact With Files & Selections:**  
  Selecting code shares context with Claude so you can ask targeted questions like "What does this function do?".

- **Multi-line Input:**  
  Use `Shift+Enter` for multi-line queries.

- **Insert File References:**  
  Use `Cmd+Option+K` or `Alt+Ctrl+K` to insert file references in your conversations.

- **View & Apply Code Diffs:**  
  Claude suggests code edits which you can apply directly.

## Elixir Integration

- Use the Claude Code SDK for Elixir as a project dependency.  
- Run `mix claude.install` to set up hooks if needed.

Sample in Elixir:

```
{:ok, session} = ClaudeCode.start_link()
{:ok, response} = ClaudeCode.query(session, "Review my mix.exs file", allowed_tools: ["View", "Edit"])
```

## AI-Enhanced Development Workflow

1. **Start with a PRD and Meeting Transcription:**  
   Create or enrich your product requirements document using AI summaries.

2. **Create an Implementation Plan:**  
   Have Claude research your project files and draft a plan in Markdown stored locally.

3. **Collaborate and Refine:**  
   Review and modify the plan as architect; iterate with Claude.

4. **Implement with Testing:**  
   Use a fresh context to implement the plan; write and review tests for correctness.

5. **Maintain Plan Persistency:**  
   Keep the PRD and plan updated for future bug fixes or changes.

## Tips

- Use one Markdown file for the plan to persist context.  
- Always review AI-generated tests carefully.  
- Restart contexts as needed to avoid context loss.

## Additional Resources

- [Claude Code SDK for Elixir Documentation](https://hexdocs.pm/claude_code/readme.html)
- [VSCode Integration Setup Guide](https://community.aws/content/2v5sof1SSJ89ke01CbCE6o4GZKz/setting-up-claude-code-in-vs-code)
- ThinkingElixir Podcast Episode:

{{< youtube w9TZnnbUZB8 >}}
