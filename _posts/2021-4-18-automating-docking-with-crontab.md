---
layout: post
title: Turning Off Applications on my Mac When Not Plugged In To Save Power
categories: Misc 
excerpt: Tips to make crontab work with MacOS Big Sur 
---

## Why Bother to Automate?

There are a number of apps that I only have running when my computer is plugged in. These are [SensibleSideButtons](https://sensible-side-buttons.archagon.net) to allow for third-party mice to use forward and back buttons in macOS and [Scroll Reverser](https://pilotmoon.com/scrollreverser/) to *only* invert the scroll direction of my mouse (not my trackpad). There are no side-effects to having them run in the background even if I use my computer without a mouse, but I'd like to reduce the number of background tasks I'm running while on battery power.



Of course, I could be much more deliberate with killing even more background tasks when I'm not plugged in to conserve as much power as possible (quitting MalwareBytes for example). However, my cronjob is so simple that it would be extermely easy to add additional apps to kill as I see fit.

## What is `cron`?

`cron` is a unix utility that allows you to schedule commands or scrips to run at specific intervals or dates. It's both simple and powerful.

Potential Uses:

- `brew update && brew upgrade` at night
- regularly check for program updates
- turning a server online and offline at certain periods of the day
- forcing a system restart every day
- running a daily web crawler

The rest is up to your imagination...

## How Do I use `cron`?


The `cron` utility executes **cronjobs** which exist in the **crontab** file. To edit the crontab file, type the following command in your terminal.
```
crontab -e
```

The syntax for each cronjob is as follows:

There are five elements that set the time and frequency of a job. 

```
minute hour day(month) month day(week)
```

The `*` symbol can be used to declare any value and the `/` symbol allows it to be a step value (ex. setting `/5` will mean *every* 5 minutes).

[crontab guru](https://crontab.guru/) is a great cron schedule expressions editor and has a brief overview of the syntax. It's a great way to sanity check your cronjobs.

## My Script

```
*/5 * * * * bash ~/Documents/programming/myutils/PluggedInMonitor.sh >/dev/null 2>&1
```

``` bash
#!/bin/bash
#PluggedInMonitor.sh

# This checks if the mac is plugged in!
if [[ $(pmset -g ps | head -1) =~ "AC Power" ]]; then
    if !(ps aux | grep "SensibleSideButtons"| grep -v grep > /dev/null); then
        # open if not running
        open "/Applications/SensibleSideButtons.app"
    fi

    if !(ps aux | grep "Scroll Reverser"| grep -v grep > /dev/null); then
        open "/Applications/Scroll Reverser.app"
    fi

#if not plugged in, we kill these apps
else 
    killall "Scroll Reverser"
    killall "SensibleSideButtons"
fi
```

## Tips

- Turn on full disk access in macOS otherwise crontab doesn't even run. [How and Why?](https://medium.com/macoclock/automate-running-a-script-using-crontab-on-macos-88a378e0aeac)
- Why `>/dev/null 2>&1`? All ouput (including stderr) gets directed to `/dev/null` so in the case there is an error (ex. there is no process for `killall` to kill), it will not be reported to my mutt mailbox. This was annoying until I redirected the output.

