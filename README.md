# rmt-tournaments

Async multiplayer tournament exchange for the RMT playtest. This repo is the
"server": the game reads tournaments and results from raw URLs, and playtest
builds upload entries here via the GitHub API.

## Layout

```
tournaments.json                       # array of TournamentDef — what players see in-game
entries/<tournamentId>/<playerId>.json # one submitted entry per player
results/<tournamentId>.json            # published results (game renders bracket + replays)
```

## Flow

1. The organizer authors a tournament and publishes it here
   (`node scripts/publish-tournament.mjs` in the game repo, or edit
   `tournaments.json` by hand — it is validated on publish).
2. Players' games fetch `tournaments.json`, show the tournament on the region
   map, and upload entries to `entries/<tournamentId>/` before the deadline.
3. At the deadline the organizer runs
   `node scripts/run-async-tournament.mjs` in the game repo against a pull of
   this repo, then publishes `results/<tournamentId>.json` back here.
4. Players' games fetch the results, render the bracket, and can replay any
   match deterministically from its seed.

Entries are trusted (friends-and-family playtest) — validation catches
accidents, not cheaters.
