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
