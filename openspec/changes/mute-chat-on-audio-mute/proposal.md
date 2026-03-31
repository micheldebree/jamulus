## Why

When a user mutes another user's audio stream in Jamulus, the chat messages
from that muted user are also suppressed (replaced with "..."). Some users want
this behavior - muting a user should hide both their audio AND their chat
messages, so they don't see text from people they've silenced.

## What Changes

- Client-side only: hide chat messages from muted users
- Extract sender name from existing chat message (which already includes name)
- Store muted user names alongside channel ID when mute state changes
- No server changes, no protocol changes

## Capabilities

### New Capabilities

- `chat-mute-on-audio-mute`: When a user mutes another user's audio, their chat
  messages are also hidden (displayed as "...") - client-side only

### Modified Capabilities

None

## Impact

- Client-side chat display logic in `CClientDlg::OnChatTextReceived` and/or `CChatDlg`
- Requires access to mute state when displaying chat messages
