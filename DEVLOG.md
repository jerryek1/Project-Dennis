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
