# Video Guide ( deprecated )
More than anything, this is a place to compound all my thoughts, ideas or findings relating to video recording/creation/streaming.
Seeing as I mainly use OBS and not Shadowplay/Share, I'll be focusing on that.
Personally I've encountered way too many issues with Shadowplay, and it honestly just isn't as flexible as OBS.
### Examples
[360fps -> tmix -> 60fps -> voukoder](https://youtu.be/-We6ixtYrbE)

[240fps -> tmix -> 60fps -> voukoder](https://youtu.be/eB4CjL2svH4) ( Thanks Owen )

[960fps -> resample -> 29.97fps -> break](https://youtu.be/PPXB2FWQ8t4) ( Thanks Break ( Only showcasing the Render settings ))

## Recording Settings
### Recording Tab
Variable | Value | Reasoning / Description
------------ | ------------- | ------------
**Recording Format** | MKV | Main reason to use MKV over other containers is that if an error occurs while writing to the file, the rest of the file ( before the error occured ) is still useable. Also it supports multiple Audio Tracks, and is really quick to remux into MP4.
**Audio Tracks** | 1-3 Ticked | Track 1 for Desktop sound + Microphone, Track 2 for just Desktop sound, and track 3 for just the Microphone - Nothing special, just personal preference.
**Encoder** | NVENC H.264 (new) | Currently it's the most ideal for my personal use case as there's minimal cpu overhead which allows me to still maintain as high fps in-game as I can.
**Rate Control** | CQP | Essentially it's just a dynamic bitrate control that tries to achieve the same level of quality between frames. I could do CBR, but this is more efficient when it comes to mass storage of clips which I tend to do a lot ^.^'
**CQ Level** | 18 | This is mostly system specific or personal preference so I recommend you play around with this value to suit you. I chose 18 as it's the most stable number I can use while still outputting 360fps clips to a level of quality that I'm happy with. If I were to aim for a lower FPS that isn't as demanding to record, I'd go with CQ 14-18 at 240fps personally.
**Keyframe Interval** | 0 | This is a personal preference, by default it sets to a keyframe every 120 frames ( 2 seconds ), but with auto it stays at the 2 seconds mark regardless of the recording fps you use ( which fits my use case as sometimes I switch to 240fps based on the game ( overwatch for example ).
**Preset** | Max Quality | It's either this or Quality. The fidelity difference between the two is marginal at most, but Max can perform better at lower bitrates from personal testing, and seeing as I'm using CQP which dynamically drops the bitrate to match the previous frames level of quality, this is better for my own use case. Main reason why it performs better at lower bitrates is due to 2-pass encoding.
**Profile** | High | So far I've found this has no real impact on performance or quality, but according to documentation it utilises flags that aren't available in the other profiles.
**Look Ahead** | Disabled | This just dynamically adjusts the number of B-Frames. It _can_ help with motion specifically, however we're already recording at fairly high quality and framerates, so it isn't needed and adds additional overhead.
**Psycho Visual Tuning** | Disabled | This enables rate distortion optimisation ( better image quality during movement ) however it also adds additional overhead while it doesn't _seem_ to impact quality significantly at CQ values lower than 21.
**GPU** | 0 | System specific, just allows you to choose a dedicated GPU to encode if you have a secondary one.
**Max B-Frames** | 0 | If you have look-ahead enabled, set it to 4, if not, set it to 0 for recording for the least amount of overhead.

### Video Settings
Variable | Value | Reasoning / Description
------------ | ------------- | ------------
**Base Res** | 1920x1080 | Canvas should ideally be the same size as your native game resolution.
**Scaled Res** | 1920x1080 | Should be the same as your base res for recordings.
**FPS** | Fractional, 360/1 | Personal preference, I choose to record at this framerate as it gives nice motionblur once my tmix filters are applied. While streaming, I may have to adjust this to 240/1, otherwise it's a bit too taxing to have Replay Buffer, Virtual Camera ( to interface between the two obs clients ) and Streaming all on at the same time.

### Replay Buffer Settings
Variable | Value | Reasoning / Description
------------ | ------------- | ------------
**Max Replay Time** | 240s | 4 minutes is long enough to capture most OW teamfights just incase you're fighting the entire time and wanted to clip something earlier.
**Max Memory** | 8192MB | I recommend setting this no more than half the amount of your RAM.

In the future I'll be playing around with settings more and being able to get actual quantitative data for each of the settings and how they impact performance along with visual fidelity.

## Streaming Settings
This works well specifically for my setup, and if you don't have a particularly strong CPU, I'd advise to use some NVENC based settings which I'll add after the CPU x264 settings. If you're only able to stream at lower bitrates, try to stick to **NVENC** encoding as it performs better at lower bitrates. 

Also, if you don't have much network bandwidth, ie <20Mbps, use NVENC on Twitch specifically. Twitch uses the source quality of your stream, however youtube does live transcoding and will *almost* always give you lower quality due to their own compression algorithms.

Twitch also has a soft-cap for non-partners at 8Mbps. It is indeed possible to stream up to 14.7Mbps as a non-partner, but only viewers who join before the livestream starts will be able to view at this bitrate. Anyone who joins later will get a stream that's been compressed down to ~6Mbps.

### CPU Encoding Settings
Variable | Value | Reasoning / Description
------------ | ------------- | ------------
**Encoder** | x264 | Personally I record already using NVENC at the same time, and due to the way OBS works, I don't want to use additional PCI-e bandwidth for this.
**Enforce Settings** | Disabled | Allows you to increase your bitrate past 6500, the limit for non-partnered streamers is 8000Kbps.
**Rescale Output** | Unchecked | Personal preference mainly.
**Rate Control** | CBR | Most streaming platforms require you to use CBR. Just determines the rate at which the frames will be encoded.
**Bitrate** | 8000 | Ideally should be under ~75% of your upload speed, but use trial and error to see what works well for you. Personally I get ~16Mbps upload so 8Mbps is fine for me to stream at.
**Custom Buffer Size** | Disabled | Most streaming services don't utilise custom buffer sizes.
**Keyframe Interval** | 2 | Most streaming platforms require you to use 2.
**Preset** | Slow | All depends on what your computer can handle comfortably. Personally I get ~10% cpu usage on OBS with the Slow preset and I'm still tinkering to see where my system performs at its best. Ideally if your computer can handle it, I'd recommend Slow, but if you're already trying to record at a high FPS, it can be incredibly taxing for your system. I'd say just use trial and error to see what works best for your use case.
**Profile** | High | Set to High. Profile determines a group of settings in the H.264 Codec. It doesn’t impact performance and gives access to a set of features that are key to streaming.
**Tune** | ( None ) | Literally the most useless thing in OBS. :/
**x264 Options** | -> | `threads=16 rc-lookahead=60 trellis=1 direct-pred=spatial`

Breakdown of x264 Options
*   **threads=16** - x264 isn't useable past so many threads, so this flag just defines how many it can use to begin with. I'm currently still playing around with the threads value to find what works best for me in order to maintain as smooth gameplay as I can.
*   **rc-lookahead=60** - Don't go past the framerate you're streaming at. Essentially it looks at the next frames in order to determine what has changed between frames. Allows you to get better quality during motion specifically with not much performance impact at all.
*   **trellis=1** - Algorithm to reduce noise, some quality presets set this to 2 which increases compute time significantly impacts performance, this flag just limits it essentially.
*   **direct-pred=spatial** - Another algorithm which can increase compute time on certain presets. Limits it again.

### NVENC Encoding Settings
Variable | Value | Reasoning / Description
------------ | ------------- | ------------
**Encoder** | NVENC H.264 (new) | Least amount of CPU overhead, useful if you don't have a very powerful system.
**Rate Control** | CBR | Constant Bitrate, majority of streaming services require you to use CBR, this is mainly because it's easier for twitch to ingest and monitor on their side.
**Bitrate** | 8000 | Ideally should be under ~75% of your upload speed, but use trial and error to see what works well for you. Personally I get ~16Mbps upload so 8Mbps is fine for me to stream at.
**Keyframe Interval** | 2 | Most streaming platforms require you to use 2.
**Preset** | Max Quality | It's either this or Quality. Max Quality is generally better for finer details, but is slightly more intensive than Quality as it uses 2-passes rather than 1.
**Profile** | High | Set to High. Profile determines a group of settings in the H.264 Codec. It doesn’t impact performance and gives access to a set of features that are key to streaming.
**Look Ahead** | Enabled | Can help with keeping quality during faster movements especially at lower bitrates.
**Psycho Visual Tuning** | Disabled | This enables rate distortion optimisation ( better image quality during movement ) however it also adds additional overhead. Play around with it, see what works well for you.
**GPU** | 0 | Only change this if you have multiple GPU's for dedicated encoding.
**Max B-Frames** | 4 | 4 if look-ahead enabled, 2 otherwise ( Twitch requires a minimum of 2 ).

Video Settings
Variable | Value | Reasoning / Description
------------ | ------------- | ------------
**Base Res** | 1920x1080 | Canvas should ideally be the same size as your native game resolution.
**Scaled Res** | 1920x1080 | Whatever you choose to stream at.
**FPS** | common, 60 | Personal preference, you can stream up to 120fps on twitch ( but you'll have worse quality too as you get your bitrate/fps per frame ).

## Cutting Clips
Before I do anything with the clips, I extract whatever I want to keep using [Lossless-Cut](https://github.com/mifi/lossless-cut/). This allows me to minimize time spent in later stages as you wouldn't have to process as much. Just open each clip in Lossless-Cut, press I to mark the beginning, and O to mark the end. Change the output container to MP4 so it also remuxs the clip from MKV -> MP4

## Frame Blending
Of all the things I've tried, I like the look of the FFMPEG tmix filter the most and honestly one of the main reasons I like it the most is that firstly it looks clean, but also doesn't need it's own application specifically for it, it uses FFMPEG which I already use for some other things. ( Remuxing, Scaling etc ).

1. Install FFMPEG if you haven't already [( Tutorial )](https://www.thewindowsclub.com/how-to-install-ffmpeg-on-windows-10).
2. Save the `resample_all_descending.bat` .
3. Edit the `tmix=frames=6` to your desired resample rate ( recording fps/output fps -> 360/60 = 6 ).
4. Put your video files in the same folder as the .bat ( as mp4 )
5. Run the bat file
6. Output file will end with `_resampled`.

There are of course other types of weighting within tmix, feel free to experiment with them yourself.
Type | Values | Description
------------ | ------------- | -------------
Descending | "5 4 3 2 1" | Prioritises newest frame
Ascending | "1 1 2 2 3 3 4 4 5 5" | Prioritises earliest frame
No Weight | "1" | Doesn't prioritise any frames

## Render Settings
I have 2 main sets of settings, my own based on Voukoder, and Break's settings. My own are more focused on finding a nice middleground between file sizes and quality. Break's settings are more geared towards quality regardless of filesize.

#### Voukoder Settings
1. Go [here](https://www.voukoder.org/).
2. Download Voukoder and Voukoder Connector ( for your application of choice ).
3. Create a new Voukoder render preset
4. Use these settings:

Variable | Value
------------ | -------------
Codec | H.264 (NVIDIA NVENC)
Preset | High Quality
Profile | Main
Strategy | CBR
Bitrate | 72000
Buffer | 13000
Filter->Scale | 3840x2160 Lanczos
Output | Disable Faststart

#### Break's Settings
Variable | Value
------------ | -------------
Codec | Magix AVC/AAC MP4
Include Video | Enabled
Progressive Download | Disabled
Frame Size | 3840x2160
Adjust frame size | Disabled
Profile | High
Frame rate | 60
Adjust frame rate | Disabled
Field Order | None ( Progressive )
Pixel Aspect Ratio | 1.0
Reference Frames | 2
Deblocking | Disabled
RC Mode | H264_CBR
Constant Bit Rate | 240,000,000
Slices | 4
Encode Mode | Mainconcept AVC
Audio Bitrate | 320,000
Project Rendering Quality | Best
