## 1. Mute State with User Name Tracking

- [ ] 1.1 Add muted user names list to CClientDlg (e.g., `QVector<QString> vecStrMutedUserNames`)
- [ ] 1.2 Update CClientDlg::OnMuteStateHasChangedReceived to extract user name and store/remove from muted list
- [ ] 1.3 Get user name from channel info - look up how to get channel name from channel ID

## 2. Chat Message Filtering

- [ ] 2.1 In CClientDlg::OnChatTextReceived, extract sender name from chat message HTML
- [ ] 2.2 Parse the name between `<b>` and `</b>` tags in the existing message format
- [ ] 2.3 Check if sender name is in muted names list
- [ ] 2.4 If muted, call ChatDlg.AddChatText with "..." instead of actual message

## 3. Testing

- [ ] 3.1 Test chat from unmuted user displays correctly
- [ ] 3.2 Test chat from muted user shows "..."
- [ ] 3.3 Test unmute restores normal chat display
- [ ] 3.4 Verify server-side code unchanged