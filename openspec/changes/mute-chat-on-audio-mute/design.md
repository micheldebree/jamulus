## CLIENT-ONLY SOLUTION

This feature is implemented entirely on the client side. No server changes, no
protocol changes, no changes to the server binary. All filtering happens in the
client when displaying chat messages.

## Context

Currently, when a user mutes another user's audio in Jamulus, the chat messages
from that muted user are still visible. We want a client-only solution that
hides chat from muted users by matching the sender name.

The server already includes the sender's name in chat messages in the format:
`(<time>) <b>UserName</b> message text`. This gives us everything we need - no
server or protocol changes required.

## Goals / Non-Goals

**Goals:**

- On the client side, hide chat messages from muted users (display "...")
- Extract sender name from existing chat message format (no protocol changes)
- Store both channel ID and user name when tracking mute state

**Non-Goals:**

- **NO server-side changes** - Server code remains unchanged
- **NO protocol changes** - No new message types, no protocol version changes
- **NO server binary changes** - Only client code is modified
- Not persisting mute state across sessions

## Decisions

1. **Client-side filtering by name extraction**
   - Chat message format on client: `(<time>) <b>UserName</b> message`
   - Extract name between `<b>` and `</b>` tags
   - Match against muted users list (stored by name)

2. **Mute state tracking**
   - Keep track of muted channel IDs (already done via `MuteStateHasChanged`)
   - Also store the user name associated with each muted channel
   - Need to get the name from channel info when mute state changes

3. **Implementation location**
   - `CClientDlg::OnChatTextReceived` - check mute state before displaying
   - `CClientDlg::OnMuteStateHasChangedReceived` - store muted user names
   - Parse sender name from chat message HTML

## Risks / Trade-offs

- **[Risk] User renames mid-session** → If user changes name while muted, chat
  won't be hidden until mute state re-syncs (rare edge case)
- **[Trade-off] Name matching is less precise than channel ID** → But
  sufficient for typical use cases

## Migration Plan

Pure client-side change, no migration needed.
