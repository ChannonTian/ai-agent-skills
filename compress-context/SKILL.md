---
name: compress-context
description: Compress the current conversation into a dense, lossless-as-possible handoff summary that can be pasted into a new chat to continue work with a fresh context window. Use when the user types /compress, /compact, /compress-context, or asks to 压缩上下文 / 压缩对话 / 总结对话以便新开对话 / compact or summarize the chat for continuation, or says the chat is getting too long.
---

# Compress Context

Goal: produce a handoff block that lets a fresh Claude chat continue this conversation with no loss of anything that matters, at a fraction of the tokens.

Note: this cannot shrink the current chat's real context window. It produces a summary; the user starts a new chat and pastes it in.

## Procedure

1. Re-read the whole conversation, including uploaded files and tool results.
2. Keep only what changes future answers. Drop pleasantries, dead ends (keep one line on why they failed), repeated drafts (keep only the latest version), and anything already superseded.
3. Preserve verbatim: exact numbers, names, IDs, URLs, file paths, code, commands, error messages, quoted requirements, and the latest version of any artifact still in use. Never paraphrase these.
4. If the user passed arguments (e.g. `/compress focus on the API design`), weight the summary toward that focus.
5. Write the output in the conversation's main language, using the template below. Omit empty sections. No commentary before or after the block except one line telling the user to paste it into a new chat.

## Output template

````markdown
# 上下文交接 / Context Handoff
(Paste this as the first message of a new chat. Treat it as prior conversation state.)

## Goal
<What the user is ultimately trying to achieve, 1–3 lines>

## User preferences & constraints
- <style, language, format, tools, hard constraints stated by the user>

## Key facts & decisions
- <fact/decision> — <reason, if it matters>

## Current state
<Where things stand right now: what is done, what is the latest version>

## Artifacts (latest versions, verbatim)
<code / text / tables still in use, in fenced blocks>

## Rejected / failed approaches
- <approach> — <why not>

## Open questions
- <unresolved items awaiting the user or more info>

## Next step
<The single next action the new chat should take>
````

## Quality check before output

- Could a new chat continue without asking anything already answered? If not, add the missing fact.
- Is every line necessary? Delete any line whose removal would not change the next answer.
- Target: under ~15% of the original conversation length, unless verbatim artifacts require more.
