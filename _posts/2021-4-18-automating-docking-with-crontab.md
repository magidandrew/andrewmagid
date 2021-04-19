---
layout: post
title: Turning Off Applications on my Mac When Not Plugged In To Save Power
categories: Misc 
excerpt: Tips to make crontab work with MacOS Big Sur 
---

## Why Bother to Automate?

turn off power hungry apps that I only use when plugged in automatically. 


## Tips
Turn on full disk access in mac otherwise crontab doesn't even run.

https://medium.com/macoclock/automate-running-a-script-using-crontab-on-macos-88a378e0aeac

https://crontab.guru/


*/5 * * * * bash ~/Documents/programming/myutils/PluggedInMonitor.sh

``` bash
#!/bin/bash

if [[ $(pmset -g ps | head -1) =~ "AC Power" ]]; then
    if !(ps aux | grep "SensibleSideButtons"| grep -v grep > /dev/null); then
        #if not running 
        open "/Applications/SensibleSideButtons.app"
    fi

    if !(ps aux | grep "Backup and Sync.app/Contents/MacOS" | grep -v grep); then
        open "/Applications/Backup and Sync.app"
    fi

    if !(ps aux | grep "Scroll Reverser"| grep -v grep > /dev/null); then
        open "/Applications/Scroll Reverser.app"
    fi

else #if not plugged in, we kill these apps
    killall "Scroll Reverser"
    killall "Backup and Sync"
    killall "SensibleSideButtons"
fi
```
