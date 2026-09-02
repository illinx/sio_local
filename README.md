# sio_local

sio_local is a protocol for the Gameboy Advance that uses the GBA's serial in/out (SIO) communication functionality to support four local players with rumble. It is based on the Gameboy Player's SIO Rumble functionality. 

This repository contains patch and binary releases for the project.

A fork of VBA-M which supports sio_local, as well as GBP SIO Rumble, can be found at https://github.com/illinx/visualboyadvance-m/tree/sio_local

__The Spec__

Based on GBP's SIO rumble feature: https://problemkaputt.de/gbatek-gba-gameboy-player.htm

The first 12 positions are identical to the GBP rumble protocol, and then messages 13-16 contain the data for players 1-4. The response messages contain rumble in the same format as GBP rumble, but with all four players packed into a single message.

| Pos | Receive (Game) | Response (Game) | Receive (GBP) | Response (GBP) |
|---|---|---|---|---|
| 0  | `0000494E` | `494EB6B1` | `0000494E` | `494EB6B1` |
| 1  | `0000494E` | `494EB6B1` | `0000494E` | `494EB6B1` |
| 2  | `B6B1494E` | `544EB6B1` | `B6B1494E` | `544EB6B1` |
| 3  | `B6B1544E` | `544EABB1` | `B6B1544E` | `544EABB1` |
| 4  | `ABB1544E` | `4E45ABB1` | `ABB1544E` | `4E45ABB1` |
| 5  | `ABB14E45` | `4E45B1BA` | `ABB14E45` | `4E45B1BA` |
| 6  | `B1BA4E45` | `4F44B1BA` | `B1BA4E45` | `4F44B1BA` |
| 7  | `B1BA4F44` | `4F44B0BB` | `B1BA4F44` | `4F44B0BB` |
| 8  | `B0BB4F44` | `8000B0BB` | `B0BB4F44` | `8000B0BB` |
| 9  | `B0BB8002` | `10000010` | `B0BB8002` | `10000010` |
| 10 | `10000010` | `20000013` | `10000010` | `20000013` |
| 11 | `20000013` | `40000004` | `20000013` | `40000004` |
| 12 | `30000003` | `40000004` | `30000003` | `40000004` |

After the handshake is complete, messages are sent according to the following protocol:

| Bits | Width | Field | Notes |
|---|---|---|---|
| 0-3   | 4  | `0x3` marker | always `0x3` |
| 4-13  | 10 | `KEYINPUT` | same format as register 4000130h |
| 14    | 1  | `connected` | 1 = a controller is present in this slot |
| 15-16 | 2  | `controller_index` | 0-3 |
| 17-27 | 11 | reserved | reserved for future inputs |
| 28-31 | 4  | `0x3` marker | always `0x3` |

So the messages look something like this, in binary:

| Pos | Dir | Word |
|---|---|---|
| 13 | host -> GBA | `0011 rrrrrrrrrrr 00 c kkkkkkkkkk 0011` |
| 14 | host -> GBA | `0011 rrrrrrrrrrr 01 c kkkkkkkkkk 0011` |
| 15 | host -> GBA | `0011 rrrrrrrrrrr 10 c kkkkkkkkkk 0011` |
| 16 | host -> GBA | `0011 rrrrrrrrrrr 11 c kkkkkkkkkk 0011` |

#### Legend

| Symbol | Width | Field | Meaning |
|---|---|---|---|
| `r` | 11 | reserved | reserved |
| `00` `01` `10` `11` | 2 | `controller_index` | 0-3 |
| `c` | 1 | `connected` | `1` = controller present, `0` = empty slot |
| `k` | 10 | `KEYINPUT` | `0` = pressed. MSB -> LSB order: L R Down Up Left Right Start Select B A |

#### Host -> GBA messages

| Bits |  Controller | Rumble on | Rumble off | Guard |
|---|---|---|---|---|
| 31-24 | 3 | `0110 0110` (`0x66`) | `0100 0000` (`0x40`) | bits 31-30 = `01` |
| 23-16 | 2 | `0010 0110` (`0x26`) | `0000 0000` (`0x00`) | — |
| 15-8  | 1 | `0010 0110` (`0x26`) | `0000 0000` (`0x00`) | — |
| 7-0   | 0 | `0010 0110` (`0x26`) | `0000 0100` (`0x04`) | bits 3-2 = `01` |

The data for controller 0, w, is in the same position as rumble data in the GBP Rumble protocol, so in that sense it's backward compatible with GBP Rumble.

Currently, the sio_local VBA-M fork always reports all four controllers as connected, and only player 1 can receive rumble. These are due to upstream limitations and they should not affect the GBP version.

__Individual Game Notes__

Sword Of Mana

* Press P2 select to join and quit the game, when there is a companion in the party. The AI will take over when P2 is not tagged in. HUD will show in full mode when P2 is active.

Double Dragon Advance

* Play "1P Double Dragon Game" to enable couch coop mode. Other game modes are not affected.

River City Ransom EX

* Press P2 select to join and quit the game. Only active in "duo" modes (Ryan+Alex or Alex+Ryan). P2 will always control the second character, and cannot control any of the other posse members. There is currently no HUD indication when P2 is joined or not.

Mario Tennis

* Unlike the others, this is a bps patch instead of ips. The ROM seems to be completely full and I could not find any free space within the 16MB jump range.
* Start story mode and choose to play doubles, and your partner will be controlled by 2P. No other modes are touched or supported right now. There is currently no way for P2 to join/leave the game--they are always in control in this mode.

Bomberman Tournament
* "Player" is now a selectable option for each char in "1 Player" battle mode, and will use the corresponding controller.
