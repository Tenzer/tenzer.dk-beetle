title: Replacing the icon of a Mac application
date: 2016-04-30
type: blog
grouping: blog
---
I use a MacBook Pro for my daily computer needs. Not long after I got it, I installed [smcFanControl](https://www.eidac.de/?cat=40) in order to increase the idle fan speed, as the laptop otherwise got a bit too hot for my liking, even when doing non-CPU intensive tasks on the machine.

There is one problem with smcFanControl though, the menu bar entry for the program can either be the temperature and/or fan speed or just an icon. Since the icon takes up considerately less space and I don't need to know the temperature and fan speed, I opted for using the icon. It does however have a problem:

![Before image](before.png)

In case you didn't spot it, the smcFanControl on the far left is not in the same crisp, Retina resolution as the other icons are.

There's no options inside the program to change the icon, so I instead decided to have a look inside the application folder and see what I could find. In case you don't know, each program inside the Applications folders is just an ordinary folder containing the files for the program, Mac just makes it look like it's one executable behind the shiny icons.

I navigated inside `smcFanControl.app/Contents/Resources` where I found a 21x21 PNG file named `smc.png`. I tried to make a small modification to the image and restarted the program, and I could see it replaced the icon in the menu bar, bingo!

Now it was just a matter of finding a suitable replacement icon with a higher resolution. I went for [this fan icon](https://www.iconfinder.com/icons/174888/fan_icon), edited the picture to remove the stand and made sure the image still was square afterwards, and then I replaced `smc.png` with this new image and I now had this:

![After image](after.png)

Much better! I also replaced `smcover.png` with the same image, otherwise would see the old icon when you clicked it to access the program.

Note: It seems like OS X automatically applies some thresholds to the image in order to make it proper two toned, and it also scales the image for you automatically so you don't need to have the image in a specific resolution.

I hope this was useful to you. In case you have any comments to this guide or just want to get in touch with me, then find my on Twitter as [@Tenzer](https://twitter.com/Tenzer).
