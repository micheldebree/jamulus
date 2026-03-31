## ADDED Requirements

### Requirement: Client filters chat from muted users by name
The client SHALL hide chat messages from users whose audio is muted, extracting the sender name from the existing chat message format and matching against stored muted user names.

#### Scenario: User receives chat from muted peer
- **WHEN** a chat message arrives and the sender's name matches a muted user's name
- **THEN** the client displays "..." instead of the actual message text

#### Scenario: User unmutes peer and receives chat
- **WHEN** a user unmuted another user's audio channel
- **THEN** future chat messages from that user are displayed normally

### Requirement: Mute state with user name tracking
The client SHALL maintain a list of muted user names and update it when mute state changes are received from the server by extracting the channel name.

#### Scenario: Mute state changed received
- **WHEN** client receives a MuteStateHasChanged message from the server
- **THEN** client extracts the user name for that channel and stores it in muted names list

#### Scenario: User unmute removes name from list
- **WHEN** client receives MuteStateHasChanged with bIsMuted=false
- **THEN** client removes that user's name from the muted names list