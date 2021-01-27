# Video Guide ( WIP )
More than anything, this is a place to compound all my thoughts, ideas or findings relating to video recording/creation/streaming.
Seeing as I mainly use OBS and not Shadowplay/Share, I'll be focusing on that.
Personally I've encountered way too many issues with Shadowplay, and it honestly just isn't as flexible as OBS.

## Recording Settings
*   **Recording Format** - MKV - Main reason to use MKV over other containers is that if an error occurs while writing to the file, the rest of the file ( before the error occured ) is still useable. Also it supports multiple Audio Tracks, and is really quick to remux into MP4.
*   **Audio Tracks** - 1-3 Ticked, Track 1 for Desktop sound + Microphone, Track 2 for just Desktop sound, and track 3 for just the Microphone - Nothing special, just personal preference.
*   **Encoder** - NVENC H.264 (new) - Currently it's the most ideal for my personal use case as there's minimal cpu overhead which allows me to still maintain as high fps in-game as I can.
*   **Rate Control** - CQP - Essentially it's just a dynamic bitrate control that tries to achieve the same level of quality between frames. I could do CBR, but this is more efficient when it comes to mass storage of clips which I tend to do a lot ^.^'
*   **CQ Level** - 20 - This is mostly system specific or personal preference so I recommend you play around with this value to suit you. I chose 20 as it's the most stable number I can use while still outputting 360fps clips to a level of quality that I'm happy with. If I were to aim for a lower FPS that isn't as demanding to record, I'd go with CQ 16-18 at 240fps personally.
*   **Keyframe Interval** - 0 - This is a personal preference, by default it sets to a keyframe every 120 frames ( 2 seconds ), but with auto it stays at the 2 seconds mark regardless of the recording fps you use ( which fits my use case as sometimes I switch to 240fps based on the game ( overwatch for example ).
*   **Preset** - Max Quality - It's either this or Quality. The fidelity difference between the two is marginal at most, but Max can perform better at lower bitrates from personal testing, and seeing as I'm using CQP which dynamically drops the bitrate to match the previous frames level of quality, this is better for my own use case. Main reason why it performs better at lower bitrates is due to 2-pass encoding.
*   **Profile** - High - So far I've found this has no real impact on performance or quality, but according to documentation it utilises flags that aren't available in the other profiles.
*   **Look Ahead** - Disabled - This just dynamically adjusts the number of B-Frames. It _can_ help with motion specifically, however we're already recording at fairly high quality and framerates, so it isn't needed and adds additional overhead.
*   **Psycho Visual Tuning** - Disabled - This enables rate distortion optimisation ( better image quality during movement ) however it also adds additional overhead while it doesn't _seem_ to impact quality significantly at CQ values lower than 21.
*   **GPU** - 0 - System specific, just allows you to choose a dedicated GPU to encode if you have a secondary one.
*   **Max B-Frames** - If you have look-ahead enabled, set it to 4, if not, set it to 0 for recording for the least amount of overhead.

Video Settings
*   **Base Res** - 1920x1080 - Canvas should ideally be the same size as your native game resolution
*   **Scaled Res** - Should be the same as your base res for recordings
*   **FPS** - Fractional, 360/1 - Personal preference, I choose to record at this framerate as it gives nice motionblur once my tmix filters are applied. While streaming, I may have to adjust this to 240/1, otherwise it's a bit too taxing to have Replay Buffer, Virtual Camera ( to interface between the two obs clients ) and Streaming all on at the same time.

Replay Buffer Settings
*   **Max Replay Time** - 240s - 4 minutes is long enough to capture most OW teamfights just incase you're fighting the entire time and wanted to clip something earlier.
*   **Max Memory** 8192MB - I recommend setting this no more than half the amount of your RAM.

In the future I'll be playing around with settings more and being able to get actual quantitative data for each of the settings and how they impact performance along with visual fidelity.

## Streaming Settings
This works well specifically for my setup, and if you don't have a particularly strong CPU, I'd advise to use some NVENC based settings which I'll add after the CPU x264 settings.

*   **Encoder** - x264 - Personally I record already using NVENC at the same time, and due to the way OBS works, I don't want to use additional PCI-e bandwidth for this.
*   **Enforce Settings** - Disabled - Allows you to increase your bitrate past 6500, the limit for non-partnered streamers is 8000Kbps.
*   **Rescale Output** - Unchecked currently - Personal preference mainly.
*   **Rate Control** - CBR - Most streaming platforms require you to use CBR. Just determines the rate at which the frames will be encoded.
*   **Bitrate** - 8000 - Ideally should be under ~75% of your upload speed, but use trial and error to see what works well for you. Personally I get ~16Mbps upload so 8Mbps is fine for me to stream at.
*   **Custom Buffer Size** - Disabled - Most streaming services don't utilise custom buffer sizes.
*   **Keyframe Interval** - 2 - Most streaming platforms require you to use 2.
*   **Preset** - Slow - All depends on what your computer can handle comfortably. Personally I get ~10% cpu usage on OBS with the Slow preset and I'm still tinkering to see where my system performs at its best. Ideally if your computer can handle it, I'd recommend Slow, but if you're already trying to record at a high FPS, it can be incredibly taxing for your system. I'd say just use trial and error to see what works best for your use case.
*   **Profile** - High - Set to High. Profile determines a group of settings in the H.264 Codec. It doesn’t impact performance and gives access to a set of features that are key to streaming.
*   **Tune** - ( None ) - Literally the most useless thing in OBS. :/
*   **x264 Options** - `threads=12 rc-lookahead=60 trellis=1 direct-pred=spatial`

Breakdown of x264 Options
*   **threads=12** - x264 isn't useable past so many threads, so this flag just defines how many it can use to begin with. I'm currently still playing around with the threads value to find what works best for me in order to maintain as smooth gameplay as I can.
*   **rc-lookahead=60** - Don't go past the framerate you're streaming at. Essentially it looks at the next frames in order to determine what has changed between frames. Allows you to get better quality during motion specifically with not much performance impact at all.
*   **trellis=1** - Algorithm to reduce noise, some quality presets set this to 2 which increases compute time significantly impacts performance, this flag just limits it essentially.
*   **direct-pred=spatial** - Another algorithm which can increase compute time on certain presets. Limits it again.

Video Settings
*   **Base Res** - 1920x1080 - Canvas should ideally be the same size as your native game resolution.
*   **Scaled Res** - 1280x720 - Whatever you choose to stream at.
*   **FPS** - Fractional, 90/1 - Personal preference, you can stream up to 120fps on twitch.

## Cutting Clips
Before I do anything with the clips, I extract whatever I want to keep using [Lossless-Cut](https://github.com/mifi/lossless-cut/). This allows me to minimize time spent in later stages as you wouldn't have to process as much. Just open each clip in Lossless-Cut, press I to mark the beginning, and O to mark the end. Change the output container to MP4 so it also remuxs the clip from MKV -> MP4

## Frame Blending
Of all the things I've tried, I like the look of the FFMPEG tmix filter the most and honestly one of the main reasons I like it the most is that firstly it looks clean, but also doesn't need it's own application specifically for it, it uses FFMPEG which I already use for some other things. ( Remuxing, Scaling etc ).

1. Install FFMPEG if you haven't already [( Tutorial )](https://www.thewindowsclub.com/how-to-install-ffmpeg-on-windows-10).
2. Save the `resample_all_descending.bat` .
3. Edit the `tmix=frames=6` to your desired resample rate ( recording fps/output fps -> 360/6 = 6 ).
4. Output file will end with `_resampled`.

## Render Settings
I have 2 main sets of settings, my own based on Voukoder, and Break's settings. My own are more focused on finding a nice middleground between file sizes and quality. Break's settings are more geared towards quality regardless of filesize.

#### Voukoder Settings

#### Break's Settings

