# sio_local

sio_local is a protocol for the Gameboy Advance that uses the GBA's serial in/out (SIO) communication functionality to support four local players with rumble. It is based on the Gameboy Player's SIO Rumble functionality. 

This repository contains patch and binary releases for the project.

A fork of VBA-M which supports sio_local, as well as GBP SIO Rumble, can be found at https://github.com/illinx/visualboyadvance-m/tree/sio_local

__Individual Game Notes__

Sword Of Mana

* Press P2 select to join and quit the game, when there is a companion in the party. The AI will take over when P2 is not tagged in. HUD will show in full mode when P2 is active.

Double Dragon Advance

* Play "1P Double Dragon Game" to enable couch coop mode. Other game modes are not affected.

River City Ransom EX

* Press P2 select to join and quit the game. Only active in "duo" modes (Ryan+Alex or Alex+Ryan). P2 will always control the second character, and cannot control any of the other posse members. There is currently no HUD indication when P2 is joined or not.

Mario Tennis

* Start story mode and choose to play doubles, and your partner will be controlled by 2P. No other modes are touched or supported right now. There is currently no way for P2 to join/leave the game--they are always in control in this mode.
