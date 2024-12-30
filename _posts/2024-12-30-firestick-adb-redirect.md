---
layout: post
title: Firestick ADB redirect
date: '2024-12-30 01:04:06 +0000'
---
During the Christmas holidays I wanted to use my fire stick 4k max which had bought previously during amazon prime day to watch some Christmas movies with my family and I really despised the amount of ads on the home page, I had tried setting up community apps which used ADB (Android Debug Bridge) commands on the firestick to launch a custom launcher instead of amazons ad filled launcher but they had removed the ability to send these commands a few months prior to originally trying and after trying multiple apps I ended up giving in to the restrictions placed until I got fed up with it on my time off and gave it another go.

I thought if amazon had blocked ADB commands within the firestick itself then I could probably do what these apps did on a different computer instead. I first tried to do it on my PC and it turned out to be successful so I wrote a python script to look at the window the firestick is on and when it was on the home page it would then run a command to redirect to a custom launcher, it actually turned out to be simple and work incredibly well and I was happy with it, I had also came across a cool screensaver app which also didn’t work but managed to do the same system to replace amazons stock screensaver which also had ads, after creating a program I wanted I then created a
systemd service for the script on a raspberry pi so it would always be running.  

Since amazon loves to constantly force updates on the firestick once users figure out loopholes I figured the only reliable way to stop the OTA (Over The Air) updates from happening would be to setup a dns blocker (Pihole) and have it be whitelist only, this way I can make sure that the only requests being accepted are the ones I specifically tell it to, I have to be this strict due to them changing the domains which would evade blocklists. 

Simple python script utilising ADB commands 
````python
import os 
import subprocess
import time 
"""
adb connect 192.168.1.128:5555
adb command for home menu wolf launcher 
adb shell am start -n com.wolf.firelauncher/.screens.launcher.LauncherActivity

find package name 
adb shell dumpsys package | grep -i "wolf"

get current app 
adb shell dumpsys activity activities | grep mResumedActivity

launch screensaver
adb shell am start -n com.neilturner.aerialviews/.ui.screensaver.TestActivity

change sleep time (in ms, defautlt 20secs )
adb shell settings put system screen_off_timeout 1200000
--------
features
--------
When home menu is focused, run wolf launcher 
When amazon screensaver is focused, run aerialviews screensaver instead  
"""
ip = "192.168.1.128:5555"

def check_connection():
    r = subprocess.run("adb devices",shell=True, capture_output=True, text=True)
    if ip not in r.stdout:
        print("not connected ")
        r = subprocess.run(f"adb connect {ip}",shell=True)


def check_activity():
    r = subprocess.run("adb shell dumpsys activity activities",shell=True, capture_output=True, text=True)
    for line in r.stdout.splitlines():
        if "mResumedActivity" in line:
            return line
    return False
 
def handle_activity():
    while True:
        time.sleep(1)
        check_connection()
        activity = check_activity() 
        
        if activity:
            # if your on amazons homepage redirect to wolf launcher
            if ".ui.HomeActivity_vNext" in activity:
                print("redirecting to wolf launcher")
                subprocess.run("adb shell am start -n com.wolf.firelauncher/.screens.launcher.LauncherActivity",shell=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)        

            # if you are on amazons screensaver redirect to desired aerialviews screensaver 
            if "u0 com.amazon.tv.ftvambient/.MainActivity" in activity:
                print("redirecting to aerialviews")
                subprocess.run("adb shell am start -n com.neilturner.aerialviews/.ui.screensaver.TestActivity",shell=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)        
            
            # if youre on aerial simulate  OK  button to trick amazon into thinking you are "active" otherwise it would take you back to the unwanted one 
            if "com.neilturner.aerialviews/.ui.screensaver.TestActivity" in activity:
                subprocess.run("adb shell input keyevent 23",shell=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)        


handle_activity()

````