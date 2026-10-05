---
name: "letta-unanswered-messages"
description: "Reminds the agent once when a turn ends with channel messages it never replied to."
---

# Unanswered channel messages

## When to use

Use this mod for agents that talk to people through channels (Discord, Telegram, Slack and others) and sometimes end a turn without calling `MessageChannel`.

## Behavioral contract

- Inbound `<channel-notification>` blocks in a turn mark their `source` + `chat_id` as pending.
- A successful `MessageChannel` call with action `send`, `send-rich` or `upload-file` clears pending entries for that channel and chat. Without `chat_id`, it clears every pending entry for that channel (the default route).
- At `turn_end`, if anything is pending and no reminder has been sent for these messages, the mod returns `{ continue }` with a reminder wrapped in `<unanswered-channel-messages>`. The reminder lists the chats and says that ending the turn is fine if silence was intentional.
- The mod's own reminder turn is ignored by its `turn_start` handler. After one reminder, pending entries are cleared at the next `turn_end`, so it can't loop.
- Entries older than 30 minutes are dropped.

## Capabilities

`events.turns`, `events.tools`. No tools, commands, network or filesystem access.

## Adapting

- Change `SEND_ACTIONS` if a channel adds another reply action.
- Change `STALE_AFTER_MS` to forget pending messages sooner or later.
- Edit the reminder text in the `turn_end` handler to match your agent's voice.
