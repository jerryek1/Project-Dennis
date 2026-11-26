**Dev Update – November 25, 2026**

**First live test of server 1.0.9**
- Brought the 1.0.9 dedicated server fleet online on GameLift for a private two-player wager test, using the 1.0.8 client. Free-for-all, most kills wins, and each player stakes 1 DENNIS + 2 ADA.

**Never-pays edge case**
- On the first attempt, one player's deposit was sent back to their own wallet instead of the pot, so the 15-minute deposit deadline passed.
- That became a real test of the never-pays case. The server cancelled the match, the player who did pay was automatically refunded on-chain (DENNIS and ADA), and the session shut down cleanly. Passed.

**Funded match**
- On the second attempt, both deposits confirmed. Each needed 10 confirmations, which takes a few minutes, so there is a short wait before the match starts. The match was marked funded and started automatically.
- Final score: 14 kills to 7. The server settled the wager and paid the winner on-chain (2 DENNIS plus the minimum ADA that has to go with it). The leftover ADA stays in the pot.

**Post-round shutdown**
- The 1.0.9 shutdown fix worked. After the round ended and the lobby emptied, the server cancelled and refunded the automatic rematch wager and ended the session about 20-25 seconds later, so no idle server time was wasted.
- The fleet scaled back to zero after testing.

**Bugs found**
- The in-game score display stayed at 0 on the clients even though the server counted kills correctly. The score widget likely binds to the player state before map travel finishes. Needs a client fix and rebuild.
- The lobby had no match map set, so the match fell back to the default third-person map. The lobby game mode's travel map needs to be set.

**Next up**
- Fix the score HUD and the match map.
- Then test the single-player and everyone-leaves edge cases.
- Then polish the wager status info in the lobby.

**Dev Update – November 25, 2026**

**Post-match server time**
- Looked into why the cloud server seemed to stay on after Sunday's wager test. The game session actually ended on its own 2 minutes after the last player left. The cost came from the idle server instance staying up until capacity was scaled to zero.

**Shutdown fix (server 1.0.9)**
- After a finished round (settled or cancelled), if the lobby is empty the server now cancels any pending rematch wager. Players who paid get refunded by the backend, and the session ends after 20 seconds instead of 2 minutes.
- A rematch wager is now created only when a player is actually in the lobby, so we don't leave orphan wagers behind. The first round still keeps the 2-minute reconnect grace.

**Cloud fleet**
- Built and packaged dedicated server 1.0.9 and swapped the cloud fleet over to it. Matchmaking now points at 1.0.9. The existing 1.0.8 game client stays compatible.
- Cloud servers idle at zero between tests, with an hourly auto-off safety check.

**Next up**
- Live test of 1.0.9.
- Then edge cases: a player who never pays, a single player, and everyone leaving.
- A more informative wager and lobby screen.

**Dev Update – November 24, 2026**

**Cloud server 1.0.8**
- Packaged and deployed server and game build 1.0.8 to a fresh cloud dedicated-server fleet, replacing 1.0.7.

**Lobby deposit countdown**
- The lobby now shows a wager deposit countdown, so players can see how long they have to fund their stake. The deposit window is 15 minutes.

**First full wager round**
- Ran the first full wager round on 1.0.8 in a private two-player test. Both players deposited 1 DENNIS + 2 ADA, and the match started only once the wager was fully funded.
- Free-for-all, most kills wins. The final score was 8-3. The server settled the result and paid the winner automatically on Cardano mainnet within minutes.
- The full flow checked out: open, funded, in progress, settled, and automatic payout.

**Next up**
- The game server stays up too long after a match is settled. Fixing the post-match shutdown is next.
- After that, edge-case tests: a player who never pays, only one player joining, and everyone leaving early.
- A more informative wager status screen in the lobby.

**Dev Update – November 22, 2026**

**Cloud server and client**
- After the GameLift crash workaround, we shipped and tested dedicated server builds through 1.0.7. Login, matchmaking, and joining a cloud match now work with wagers enabled.
- The client automatically retries joining a session a few times if the first attempt fails, and it shows a clear message when no cloud servers are available.
- A full cloud test on 1.0.7 completed cleanly: players could join, and the session shut down properly when the match ended.

**Automatic wagers (private tests)**
- Automatic refunds and payouts are now on for private testing only. Public play is not open yet.
- Wager flow: each player stakes 1 DENNIS + 2 ADA. Matches are free-for-all, and most kills wins. If a match does not fill in time, it is cancelled and everyone is refunded automatically.
- Fixed a payout bug that blocked automatic settlement when a match had more than one deposit.
- We completed the first fully automatic wager payout in a private live match. After the round ended, the winner's DENNIS and ADA arrived in their Cardano wallet within seconds, with no manual steps.

**Next up**
- Keep running private live tests of automatic refunds and payouts.
- Longer term: upgrade to the 3.x GameLift plugin.

**Dev Update – November 16, 2026**

**Lobby wager UI**
- Finished wiring the lobby wager UI in WBP_LobbyOverlay: the join wager button, the status text that updates on join, status polling every 10 seconds, and error messages shown in the lobby.

**First cloud match test**
- Ran the first real match on the cloud dedicated server. Login and matchmaking worked end to end, and the client reached the cloud server.
- The server crashed whenever a match started with wagers enabled. Traced it to a memory allocator mismatch in the GameLift server SDK (5.1.3) used by our older GameLift plugin: its fleet-role-credentials call frees memory allocated inside the SDK DLL with Unreal's allocator. AWS fixed this upstream in plugin 3.0.0 / SDK 5.3.
- Wrote a workaround in the wager server subsystem that calls the SDK directly, avoids the faulty cleanup, and adds step logging.

**Next up**
- Build server 1.0.4, redeploy, and retest with a small test wager.
- Longer term: upgrade to the 3.x GameLift plugin.

**Dev Update – November 14, 2026**

**Wager system (Dennis Token on Cardano)**
- The backend for Dennis Token wager matches is now live on AWS. It handles joining a match, checking match status, and a relay so the game never holds our blockchain API key.
- A deposit scanner checks the prize pot every minute and matches incoming deposits to registered players.
- Game servers report match lock, start, result and cancel to the backend through signed requests that only our server fleet can make.
- Rules: free-for-all with 2–20 players, and most kills wins. Ties go to fewest deaths, then the pot is split. No house fee.

**Game client & server**
- Wager code is now built into the UE5 client and the dedicated server. It's switched off by default until launch testing is done.
- The player name now comes from the login token instead of the text typed at sign-in.
- Logs no longer print auth tokens, only their length.
- New dedicated server build 1.0.3 is uploaded to GameLift, with all recent server changes compiled in.

**Next up**
- Spin up a cloud server fleet for build 1.0.3.
- First real test deposit through the in-game wallet.
- Payout dry run, then launch prep.
