# Game Boy Advanced

## Pinout

32pin cartridge slot.

The cartridge bus may be used for both CGB and GBA game paks. In GBA mode, it is used as follows:

| Pin   | Name   | Dir | Description                                                |
| ----- | ------ | --- | :--------------------------------------------------------- |
| 1     | VDD35  | ⟸  | Power Supply (3.3VDC/5VDC)                                 |
| 2     | PHI    | ⟸  | System Clock (selectable none, 4.19MHz, 8.38MHz, 16.78MHz) |
| 3     | /WR    | ⟸  | Write Select                                               |
| 4     | /RD    | ⟸  | Read Select                                                |
| 5     | /CS    | ⟸  | ROM Chip Select                                            |
| 6-21  | AD0-15 | ⟺  | lower 16bit Address and/or 16bit ROM-data (see below)      |
| 22-29 | A16-23 | ⟺  | upper 8bit ROM-Address or 8bit SRAM-data (see below)       |
| 30    | /CS2   | ⟸  | SRAM Chip Select                                           |
| 31    | /REQ   | ⟹  | Interrupt request (/IREQ) or DMA request (/DREQ)           |
| 32    | GND    | --  | Ground 0V                                                  |

*Note: Direction is cartridge relative Game Boy.*

### GBA Game Pak SRAM

A 16bit address is outputed through AD0-AD15, then 8bit of data are transferred through A16-A23.

### GBA Game Pak ROM

24bit address is output through AD0-AD15 and A16-A23, then 16bit of data are transferred through AD0-AD15. The 24bit address is formed from the actual 25bit memory address (byte-steps), divided by two (halfword-steps).

### Game Pak 8-bit

In 8-bit mode 5V is used. [GBA](http://hardwarebook.info/GBA "GBA") & [GBA SP](http://hardwarebook.info/GBA_SP "GBA SP") have a mechanical switch which the card pushes to select between 3V or 5V.

In 8bit mode, the cartridge bus works much like for GBA SRAM, however, the 8bit /CS signal is expected at Pin 5, while GBA SRAM /CS2 at Pin 30 is interpreted as /RESET signal by the 8bit MBC chip (if any). In practice, this appears to result in 00h being received as data when attempting to read-out 8bit cartridges from inside of GBA mode.

## Compatibility

Compatibility for differents Game Pak type:

|                                                                                                              | Original | Dual Mode | Color only | Advanced only |
| ------------------------------------------------------------------------------------------------------------ | -------- | --------- | ---------- | ------------- |
| [Game Boy](http://hardwarebook.info/GB "GB")                                                                       | OK       | OK        | -          | -             |
| Game Boy Pocket                                                                                              | OK       | OK        | -          | -             |
| [Game Boy Color](http://hardwarebook.info/GBC "GBC")                                                               | OK       | OK        | OK         | -             |
| [Game Boy Advance](http://hardwarebook.info/GBA "GBA") / [Game Boy Advance SP](http://hardwarebook.info/GBA_SP "GBA SP") | OK       | OK        | OK         | OK            |
| Nintendo[GameCube](http://hardwarebook.info/GameCube "GameCube") Game Boy Player                                   | OK       | OK        | OK         | OK            |
| [Game Boy Micro](http://hardwarebook.info/GBM "GBM")                                                               | -        | -         | -          | OK            |
| [Nintendo DS](http://hardwarebook.info/NDS "NDS") / [Nintendo DS Lite](http://hardwarebook.info/NDSL "NDSL")             | -        | -         | -          | OK            |

## Game PAK ROM

16-bit with variable size: This is the place where the cartridge ROM is accessed.
While it may provide one of the slowest rates, it’s also mirrored in the memory map to manage different access speeds.
Additionally, Nintendo fitted a Prefetch Buffer that interfaces the cartridge to alleviate excessive stalling.
This component independently caches continuous addresses when the CPU is not accessing the cartridge, it can hold up to eight 16-bit words.
In practice, however, the CPU will rarely let the Prefetch Buffer do its job.
Since by default it will keep fetching instructions from the cartridge to continue execution [20] (hence why IWRAM and EWRAM are so critical).

## Game PAK RAM

8-bit with variable size: This is the place where the cartridge RAM (SRAM or Flash Memory) is accessed.
This is strictly an 8-bit bus (the CPU will see ‘garbage’ in the unused bits) and for this reason, Nintendo states that it can only be operated through their libraries.

## References

[Game Boy Advance Architecture](https://www.copetti.org/writings/consoles/game-boy-advance/)
[Game Pak](http://hardwarebook.info/Game_Pak)
[GBATEK](https://problemkaputt.de/gbatek.htm#gbatechnicaldata) Gameboy Advance / Nintendo DS / DSi / 3DS - Technical Info - Extracted from no$gba version 3.05
