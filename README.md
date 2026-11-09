# PhoneMP

A small multiplayer mod for Unity IL2CPP games on Android, built with [MelonLoader](https://melonwiki.xyz).
It was made for a mobile Granny setup (`com.OmegaMegaGigalIntel.GrannyLegacySpeed`) and is designed to be touch friendly: big buttons, an on-screen keypad and no keyboard shortcuts.

Players join a room, see each other as capsules, chat, and vote to restart the game together.

> **Status:** hobby project. Positions and chat go through a tiny UDP relay (`relay.py`). There is no anti-cheat and no authentication, so only play with people you trust.

## Features

- **Rooms:** every player starts in a random 4-digit room. Tap **Room Code** and type a code to join someone else.
- **Public games:** tap **Public** to list your room. Other players open **Games** and tap a room to join.
- **Restart vote:** the top-left button shows `Restart votes/players`, for example `0/1` alone and `0/2` with two players. When everyone has voted, the game restarts. Tap again to take your vote back.
- **Chat:** type a message or use the quick buttons (`Hi!`, `Help!`, `Run!`, `GG`). Recent messages show on the left.
- **Hide / Open:** one button on the right hides every other mod button and panel. It changes to **Open**, and tapping it brings everything back.
- **Remote players:** other players show as red capsules that move smoothly between updates.
- **Custom look:** rounded buttons, colors, outlines and text shadows, all generated in code with no asset files.

## What it does not do (yet)

- No name tags above players.
- No player rotation. The facing direction is sent over the network but not used.
- No character model or animation, only capsules.
- No collision with remote players.
- Not tested with more than a few players.

## Requirements

- An Android game with **MelonLoader** installed (IL2CPP).
- A relay server that everyone can reach (see [Relay](#relay)).

## Install

1. Download `PhoneMP.dll` from the Releases page.
2. Copy it into the game's MelonLoader `Mods` folder, for example:

   ```
   MelonLoader/com.OmegaMegaGigalIntel.GrannyLegacySpeed/Mods/PhoneMP.dll
   ```

3. Fully close and reopen the game so MelonLoader loads the new DLL.

Everyone who plays together should use the same version of the mod.

## Settings

On first launch the mod creates a `PhoneMP` section in MelonLoader's preferences file:

| Setting  | Default                          | Meaning                                |
|----------|----------------------------------|----------------------------------------|
| `Server` | `chords-utp.tun.ply.gg:19236`    | Relay address as `host:port`           |
| `Name`   | `Player` plus a random number    | Your name, shown in chat               |

Change `Server` to your own relay address if you host one.

## Relay

`relay.py` is a small UDP relay with no dependencies beyond Python 3. It forwards position and chat packets between players in the same room.

**Download:** [relay.py](https://github.com/kshvekbdhe589-lgtm/Granny-legacy-Mp-Mobile-Mantience-/releases/download/v0.3.0-beta/relay.py)

```bash
python3 relay.py          # listens on UDP 47777
python3 relay.py 12345    # or choose a port
```

Notes:

- The first player in a room is its owner, and only the owner's **Public** setting decides whether the room is listed.
- A room holds up to 16 players. A player who stops sending data for 10 seconds (for example, they closed the game or lost connection) is dropped.
- The relay needs to be reachable from the internet if players are on different networks. Run it on a server with an open UDP port, or put a UDP tunnel such as [playit.gg](https://playit.gg) in front of it.
- Running the relay in Termux works. Run `termux-wake-lock` so Android does not stop it.

### Packet format

Every packet starts with magic byte `0x47`, then a type byte:

| Type | Meaning                                                             |
|------|---------------------------------------------------------------------|
| `1`  | Position: room, public flag, id, name, x, y, z, yaw, restart vote   |
| `2`  | Request the public room list                                        |
| `3`  | Room list reply                                                     |
| `5`  | Chat: same header as position, then the message                     |

## Publishing note

The `refs/` DLLs come from the game and MelonLoader, so they are not yours to redistribute. Do not commit them. A suggested `.gitignore`:

```
bin/
obj/
refs/
```


## Project layout

```
PhoneMP.cs        the mod (networking, vote, chat, UI)
PhoneMP.csproj    project file
relay.py          UDP relay server
```

## License

Add a license of your choice here, for example MIT.
