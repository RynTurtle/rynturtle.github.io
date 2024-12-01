---
layout: post
title: Spotify & Itunes API wallpaper changer
date: 2024-12-01 19:48 +0000
---
![example #1](/assets/img/wallpaper-1.jpg){:width="200" .left}![example #2](/assets/img/wallpaper-2.jpg){:width="200" .left}


[GitHub link](https://github.com/RynTurtle/album_wallpaper)
<h3> How It started/What it does</h3>
Before I started university I wanted to get a head start in learning C++, I had an idea in my mind about a program which uses the Spotify API to get the album covers from my liked song list and add that as my wallpaper, I originally created it with python just as a proof of concept and whilst testing I realised the covers were only 640x640 pixels which means the quality of the wallpaper would be pretty bad. After researching I found a post by [bendodson](https://bendodson.com/projects/itunes-artwork-finder/) this website seemed to have very high quality images so I wanted to find out how he did this. The website luckily had the source code linked on GitHub, after a while of poking around the php code I could see how he accomplished it.
I saw he got the ID of the album then used a different link with the album id and it would return the higher quality image so I tested it in the python version of the wallpaper changer, when I got it working I decided to now try and recreate it in C++ with a more organised structure and better coding practices as best as I could at the time. 

<h3> FFmpeg </h3>
Another aspect of my wallpaper changer program is the blurred affect background behind the album image, this was done using [FFmpeg](https://ffmpeg.org/), a command line tool used processing of media including video, music, audio etc, FFmpeg is used in programs like OBS and streaming or video services like Twitch/Youtube.  The FFmpeg command I used included the -filter_complex flag which I inserted the original image and added the boxblur affect, After a while of changing the values I found  the nice blur affect I wanted. 

<h3> Future of the project </h3>
After starting University, I can now see I can improve the code and I intend to in the future, I should add more error handling to ensure the program doesn’t close since it does happen sometimes. 
