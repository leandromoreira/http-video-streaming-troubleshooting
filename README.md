# A troubleshooting guide for video streaming issues

This is a collection of issues related to video streaming, how to check them and, when possible, how to solve them. If you work with video streaming (either live or VOD) for at least two years, [you know what it's like!](https://haasn.xyz/posts/2016-12-25-falsehoods-programmers-believe-about-%5Bvideo-stuff%5D.html) **It's a shame we can't just say it works on [VLC](https://github.com/videolan/vlc) and move on!**

The many problems arise, mostly, from our silly wish to delivery video throughout all of the devices: 
* many browsers (firefox, chrome, edge, ios safari, android browser ...)
* many OSs (linux, macos, windows, ios, android ...)
* many TVs brands (samsung, LG, sony, TCL ...)
* many TVs brands models or release year (2012, 2013, 2016 ...)
* and the multiplication of these factors: my **video (delivered over Z protocol)** doesn't play well in an **X mobile at a Y version** using the **K browser at version X**

We'll focus mainly on [HTTP adaptive bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) protocols and the mostly used [CODECs](https://github.com/leandromoreira/digital_video_introduction#how-does-a-video-codec-work).

> **Disclaimer**: the problems and solutions found here were faced and challenged by many of my colleagues so **you are welcomed to contribute with your story too** :mortar_board: or even helping (correcting) us to understand some of the spooky problems. 

# HTTP cookies don't work everywhere!
> The old n' good cookie HTTP header aren't edible in some TVs!

Nope, some TVs doesn't know or care about what [cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies) do so **the path might be your only savior.**

# HTTPS doesn't perform well in all TVs
> To be or not to be HTTPS?

Some old TVs doesn't perform well under [HTTPS](https://en.wikipedia.org/wiki/HTTPS), there will be many dropped frames or even unplayable videos. Sometimes what **one needs to do is to be insecure!** PS: you might need to be insecure even for images.

# CORS is not followed in all devices
> Some TVs doesn't respect the police

Some TVs also don't respect or work with [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)!

# Do not mix video frame rate
> Mixed frame rate isn't a problem in 2018? is it?

Well, some TVs doesn't like to mix different [frame rates](https://en.wikipedia.org/wiki/Frame_rate). For instance, we had our first rendition using 15 FPS while our bigger renditions were using 30 FPS, nice and tide for almost all of the devices, except some TVs, they play the content but dropping a lot of frames :( yeap, **some TVs won't play great with different frame rates**, mostly when they're switching among the renditions.

# Do not mix audio frame (sample) rate
> Lip sync battle!

If your **audio framerate is not consistent** in all your renditions, some devices might **play glitches** randomly or even worst they won't play it at all, so **stick with a single audio frame rate.**

# Stick to 44khz?
> Lip sync battle 2 - the return!

Using the **same audio frame rate isn't enough**, we noticed some devices are really rooted in their causes, they **play better at 44khz.** I really don't know why.

# [PTS](https://en.wikipedia.org/wiki/Presentation_timestamp), GOP and segment size must be orchestrated
> Time, it needs time to win back your love again

Sometimes your playback will present strange behaviors like going back in time, freezing and then playing again and etc. And timing might play a big role here, HLS ,for instance, expects your [GOP's](https://en.wikipedia.org/wiki/Group_of_pictures) to be a multiplier of your segment size (and don't forget to [disable scene detection](https://en.wikibooks.org/wiki/MeGUI/x264_Settings#scenecut)). It doesn't hurt to remember that the PTS and the program date time of your renditions should be in sync.

# EXT-X-MEDIA-SEQUENCE used to sync renditions
> If it's not on the standard, I'll do what I want!

The playback needs some way to know how to switch among the renditions, they need to use some information to do that and the data that the playback uses to sync the renditions might negatively affect your final users. 

The HLS has a tag called [EXT-X-MEDIA-SEQUENCE](https://tools.ietf.org/html/draft-pantos-http-live-streaming-23#section-4.3.3.2) to indicate the media sequence number of the first media segment that appears in a playlist file.
   
It turns out that some devices and players use this tag to sync all the renditions :( even though the HLS standard in [March 2013](https://tools.ietf.org/html/draft-pantos-http-live-streaming-09#section-3.4.3) (almost 6 years ago) made it clear it MUST NOT BE USED this way.

# General steps to verify MPEG-DASH or HLS issues

* check if [cors is enabled](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) at your servers
* try to run a publicly known, same resource kind, to see if it's a general problem, like does it play a known hls/mpeg-dash url?
* try different devices, OSs and browsers to see if the problem persists
* run some analyzers, for instance, Apple offers its [mediastreamvalidator](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/StreamingMediaGuide/UsingHTTPLiveStreaming/UsingHTTPLiveStreaming.html) for HLS and there is [ffprobe](https://www.ffmpeg.org/ffprobe-all.html#Synopsis) and if you search for `"<protocol/codec> analyzer"` you might find more tools.
* check if there any official recommendation for a given protocol like, [HLS](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices#2969492) or even [general guide for MPEG-DASH](https://developer.mozilla.org/en-US/docs/Web/Apps/Fundamentals/Audio_and_video_delivery/Setting_up_adaptive_streaming_media_sources#MPEG-DASH_Encoding) they worth to read!
