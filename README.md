# stereodemux

This command-line tool converts an FM broadcast signal into stereo sound with de-emphasis applied.

It expects 16-bit signed-integer MPX (FM demodulated mono PCM) via stdin and outputs stereo PCM to stdout.

## Requires

* [liquid-dsp](https://github.com/jgaeddert/liquid-dsp)

## Compiling

    make

## Usage

This program should be used via UNIX pipes:

    ./demux -r <samplerate> [-R samplerate_out] [-d time_constant_μs] [-g gain_db]

    -r   Input sample rate (Hz).

    -R   Output sample rate (Hz). The default is to use the input rate.

    -g   Additional output gain (dB). Beware of clipping. 6 dB means doubled amplitude;
         or you could give an amplitude ratio instead by adding an 'x'.

    -d   Time constant of the de-emphasis filter (microseconds).
         Meaningful values are 75 for the Americas and South Korea and
         50 elsewhere; the default is 50.

## Examples

Listen to stereo broadcasts with `rtl_fm` and `sox`:

    rtl_fm -M fm -l 0 -A std -p 0 -s 192k -g 40 -F 9 -f 90.0M | \
      ./demux -r 192k | \
      play -q -t .s16 -r 192k -c 2 -

Listen with `aplay`, resampling inside demux:

    rtl_fm -M fm -l 0 -A std -p 0 -s 192k -g 40 -F 9 -f 90.0M | \
      ./demux -r 192k -R 44.1k | \
      aplay -f S16_LE -c 2 -r 44100

Convert a 192 kHz MPX WAV into a 44.1 kHz stereo WAV:

    sox mpx.wav -t .s16 - | \
      ./demux -r 192k -R 44.1k | \
      sox -t .s16 -r 44.1k -c 2 - stereo.wav