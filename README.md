# Tascam DP-24SD firmware study

## How it began

This beautiful digital multitrack recorder and mixer unit from Tascam is really great to use, sounds really good and have an ergonomy way better than all its competitor in this price range. However it comes with some surprising limitations when considering how high-end it is and when you know a bit about digital signal processing.

### The volume fader recall absence
Quite surprisingly, loading a "song" (equivalent of a "scene" in pure digital mixer without recording features) recalls all the channel parameters (panoramic, equalization, send levels, ...) **except** the volume fader level ! This prevents the unit to be used as a real digital mixer, whether it's for live shows or studio production. It's a bit of a pain for such a great product. It even feels like it's a kind of marketing based decision not to have the volume fader recall as it's implemented in all competing products, even less high-end (ZOOM R8 for example). Like "if you want the feature, you can buy our more expensive product which have it".

### The dynamic processors only useable on inputs and not on tracks played back
Maybe a bit more arguable limitation than the volume fader recall absence, the same kind of painful limitation for such a product seems to happen to the 8 digital dynamic processors. They are only available for signals coming from external inputs (inputs A to H) immediatly after the analog to digital conversion, but not to signals already recorded and being played back by the unit (tracks 1 to 24).

One could think of this limitation because of restricted computational power of the unit, but it seems highly unlikely. Indeed when unit can play back 24 tracks at a time, or record 8 inputs and play back 16 tracks. At the end of the day, the total amount of signals being mixed together and sent to the stereo bus will be the same, a maximum of 24 signals. Actually, the unit can even play back 24 tracks signals AND the 8 inputs signals mixed altogether... so there's little room to think about a computational power limit here.

## Is the volume fader level saved anyway ?

Before spending any time in attempting to hack the firmware in order to restore the volume fader level, the first important thing to verify is if the level is saved along with all the other parameters in the "song" file on the SD card.

### Song parameters file forensic
1. Save the current song parameters file (SPF) on my PC
2. Perform another save operation on the DP24, then save the SPF on my PC to check if some date related stuff has been added to it
3. Move the volume fader on track 1 of the DP24 to its max level, then save the SPF on my PC to check if some parts of it changed


### Results
* No difference between SPF between step 1 and step 2 -> no date/timestamp data is saved (it could have misled us)
* The SPF at step 3 has indeed been updated at 2 places, and indeed holds the volume fader value. How do we know it ? I pushed the volume fader to its maximum position (+6 db) and the value at the only location in the file that changed is now `0x7F` or 127 in decimal. This is the suspected maxint of this unit as panoramic controls vary from -63 to 63, the aux sends from 0 to 127, and so on for a lot of values.

So now we know that the volume fader position is stored indeed, and not recalling it is when loading a song is probably on purpose from Tascam engineers.


## Pre-study resources

Other tascam recorder reversed and modded https://github.com/dr05-homebrew/dr05-homebrew (can be a different platform, not the same product line)

Related event at the CCC https://www.events.ccc.de/congress/2016/wiki/Session:Reverse-engineering_the_DR05_audio_recorder

Wide firmware RE database and automatized identification http://firmware.re/

Firmware download and changelog on manufacturer website https://www.tascam.eu/en/downloads/current/DP-24SD

Firmware architecture identification tool https://github.com/airbus-seclab/cpu_rec

## Plan

Identify if the binary is encrypted/obsfuscated/compressed, and possible hardware architecture if not.
Compare the different version of the firmware that are available for download to gather information. If almost everything changes from one version to one another, it is probably encrypted or compressed.

