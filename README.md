# mGB 
mGB is a Gameboy cartridge program (You need a Flash Cart and Transfer hardware) That enables the Gameboy to act as a full MIDI supported sound module. It works with the old DMG Gameboy as well as GBC/GBA.

[More information about Arduinoboy](https://github.com/trash80/arduinoboy)

![ScreenShot](http://trash80.net/arduinoboy/mGB1_2_0.png)

## Building mGB from Source in 2022 (Makefile/sdcc toolchain)
1.  Recursively clone this mGB repo
    - `git clone --recurse-submodules https://github.com/TonyTwoStep/mGB.git`
2. I've updated the submodule for `gbdk-n` to work with newer versions of SDCC (since `sdcclib` was deprecated and replaced by `sdar`)
3.  Change into the `gbdk-n` submodule's directory and compile it
    - `cd gbdk-n && make`
    - ensure it compiles without errors
4.  Change into the Source directory of mGB
5.  Compile mGB from source
    - `make`
    - output will be in that dir, `mgb.gb`

## Change Log
* 03/01/22
  * Project was moved to this repo where I updated dependencies and the sdcc/makefile toolchain so that it builds in modern day
  * Gameboy color palettes were fixed for versions compiled with the Makefile
  * Preset saving fixed
  * Fix notes over G5 not working in MIDI channels
  * Add Rom header
  * Add ability to use a custom pause screen
 * 06/26/15 
   * Project has been moved to GitHub along with sourcecode.
 * 12/21/12 1.3.3
   * Fixed a bug with the new pitchbend resolution
   * Optimized some of the pitchbend routine
 * 12/20/12 1.3.2
   * Increased pitchbend resolution
 * 12/19/12 1.3.1
   * Fixed PU1 Envelope retrigger bug. 
 * Feb 2 2009 1.3.0
   * Rewrote 90% of code into assembly for much faster performance- especially noticeable on DMG.
   * Changed note behavior. Removed Monophonic note memory to increase performance. 
   * Envelope does not retrigger if the notes overlap and have the same velocity- Good for arpeggios / broken chords. 
   * Note off has a slight delay so immediate retrigged notes don't cause "clicking" effect due to turning off the synth. 
   * Added screen off mode for great signal-to-noise ratio, longer battery life, and better performance on DMG. (To toggle the screen mode hold Select and press A.)
   * Created back-end routine that prioritizes processes for better performance. 
   * Added 8 "noise" shapes to the Wav synth for more interesting effects.
   * Made Wav pitch sweep stable and changed it so it glitches out at values above 8. :D
 * Nov 5 2008 Version: 1.2.4
  * Fixed small bug with the indicator arrow, it was offset vertically a bit.
  * Fixed bug with unexpected behavior with large PB Ranges
  * PB Range Max is now 48 notes. (hehe)
  * Octave Shift max is now -2/+3 
  * Added some Octave shift logic. If the current note is greater than what the GB can play due to octave shifting, it will select the lower octave note, so no off key notes will play.
  * Added Gameboy Color fast-cpu mode- better performance with newer Gameboys.
 * Oct 28 2008 - Version: 1.2.3
  * Added Note markers above synth number, so you can tell if the synth is on. ;)
  * Added PB wheel reset to MIDI panic (Start button)
  * Code more efficient (I'm sure there is still more to do here)
  * nitro2k01 @ http://gameboygenius.8bitcollective.com rewrote my gamejack serial function to make it as fast as possible. THANKS!!
 * Oct 25 2008 - Version: 1.2.2
  * Added Program Change messages to mGB
  * Rewrote MIDI data input for mGB. (Rewrote the function in ASM to make it faster)
  * Added Controller Priority. While changing parameters on the gameboy itself, MIDI messages will not overwrite your changes while your editing them. This is a good live mode feature 
 * Oct 23 2008 - Version: 1.2.1
  * Found & Fixed various bugs in 1.2.0 
  * Changed help text. Made it more clear.
 * Oct 23 2008 - Version: 1.2.0
  * Change interface a bit
  * Added presets
  * Optimized code
 * Oct 20 2008 - Version: 1.1.0
  * Added Interface
  * Changed Wav CCs Around to make more consistent with the Pu Synths.
 * Oct 4 2008 - Version: 0.1.2
  * Fixed bug with Wav Synth hanging after sequencer stop. 
  * Fixed bug with Wav Synth not resetting monophonic keyboard note triggers

## Button Shortcuts
 * Start: MIDI Panic
 * Select + Dpad: Select multiple synths for editing.
 * Select + A: Toggles the screen on or off, better battery life, less noise, and faster response.
 * Select + B: Copys all parameters on screen while cursor is not on preset number.
 * B: Pastes all parameters while cursor is not on preset number
 * A + Dpad: Change parameter value 
 * To load/save presets, put the cursor on the "PRESET" number, and hit B for load, Select+B to save

## MIDI Implementation
Note: the name and number at the bottom left of the screen indicates the midi CC of the selected parameter.

 * PU1 - MIDI CH1
  * Program Change: 1 to 15
  * PB: Pitch bend - up to +/- 12
   * cc1: Pulse width - 0,32,64,127
   * cc2: Envelope mode - 0 to 127, 16 possible steps
   * cc3: Pitch sweep
   * cc4: Pitchbend Range
   * cc5: Load Preset
   * cc10: Pan
   * cc64: Sustain- Turns off note off. <64 = off, >63 = on

 * PU2 - MIDI CH2
   * Program Change: 1 to 15
   * PB: Pitch bend - up to +/- 12
   * cc1: Pulse width - 0,32,64,127
   * cc2: Envelope mode - 0 to 127, 16 possible steps
   * cc4: Pitchbend Range
   * cc5: Load Preset
   * cc10: Pan
   * cc64: Sustain- Turns off note off. <64 = off, >63 = on

 * WAV - MIDI CH3
   * Program Change: 1 to 15
   * PB: pitch bend - up to +/- 12
   * cc1: shape select : 16 possible on a 0 to 127 range
   * cc2: shape offset : 32 possible on a 0 to 127 range
   * cc3: Pitch Sweep speed. 0=Off, 1-127 speed.
   * cc4: Pitchbend Range
   * cc5: Load Preset
   * cc10: pan
   * cc64: Sustain- turns off note off. <64 = off, >63 = on

 * NOISE - MIDI CH4
   * Program Change: 1 to 15
   * PB: pitch bend +/-24
   * cc2: envelope mode - 0 to 127, 16 possible steps
   * cc5: Load Preset
   * cc10: pan
   * cc64: (sustain) turns off note off. <64 = off, >63 = on

 * POLY MODE - MIDI CH5 - Plays Pu1/Pu2 and Wav in poly
   * Program Change: 1 to 15
   * PB: pitch bend +/-2
   * cc1: See cc1
   * cc5: Load Preset
   * cc10: pan
   * cc64: (sustain) turns off note off. <64 = off, >63 = on

## Custom graphic pause screen
Assumes above build steps were done and tested before introducing more variables to the experiment
- Created a graphic in GIMP
    - 160x144 resolution
    - 4 colors only (image->mode->indexed->4 colors)
    - export as PCX (Zsoft in the file extension menu of export)
- Convert the PCX to C tile and map arrays
    - Copy PCX files into the directory where `pcx2gb.exe` is located (included in this project under `Source/build_tools`) 
    - Launch `doxbox`
    - While in DOS, execute a command like `PCX2GB.EXE o d CUSTOMGFX.PCX TILEOUT.C MAPOUT.C`
    - Press enter a few times and it will eventually spit out the two C files above
- Append these two data structures to the end of `mGB.h`
- Change their filetype from `unsigned char` to `const UBYTE`
- In the `toggleScreen()` function of `mGBDisplayFunctions.c` I made the following changes:

```
void toggleScreen()
{
        if(currentScreen == 0) {
                DISPLAY_ON;
                displaySetup(); // add this to re-setup the background tiles and cursor after showing your image
                showMainScreen();

        } else {
                currentScreen = 0;
                DISPLAY_OFF;
                set_bkg_data(0, 255, yourCustomTiles); // load the tiles
                set_bkg_tiles(0, 0, 20, 18, yourCustomMap); // set the map
                DISPLAY_ON; // turn the display on
                hideCursor(); // hide the cursor which was showing as an artifact on the pic

        }
}
```

- Compile and run to test on real hardware
- Ensure MIDI actually works too

