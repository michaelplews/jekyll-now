---
layout: post
title: Customizing Mouse Buttons in Ubuntu
---

I bought a new mouse this week [(Tecknet M002)](http://amzn.com/B001DHECXA) as my other had been acting strange. I only needed a cheap one, but this one came with two extra thumb buttons on the side. In Ubuntu by default, they seemed to go forward one page and back one page in Files and Firefox. Useful, but I thought I might see if I could change it up a bit.

I liked the idea of having the buttons Tab-left and Tab-right in Firefox. I started googling and found that it indeed could be done, however the guides I found were either outdated or seemed much too complicated. So here is how I did it:

## The Code

I started by installing programs required and dependencies:

```bash
~$ sudo apt-get install xdotool xev xbindkeys xbindkeys-config
```
(I believe these are all the dependencies, I will update if I find out otherwise)


First we need to find how the keys are read by the computer, and which key numbers have been assigned.

```bash
~$ xev | grep button
```

A small window should appear. Click over the window with the appropriate buttons you wish to change. The output should look something like this:

```
state 0x0, button 8, same_screen YES
state 0x100, button 8, same_screen YES
state 0x0, button 9, same_screen YES
state 0x400, button 9, same_screen YES
```

This shows buttons 8 and 9 are the ones clicked. Next we need to edit the source file for xbindkeys:

```bash
~$ gedit ./.xbindkeysrc
```

Editing this file in the following format will allow keyboard presses to be assigned to your mouse clicks.

```bash
# Remark 
"command" 
m:xxx + c:xxx 
Shift+...
```

In this case, my additional to this file were:

```bash
#Tab Right
"xdotool key control+Tab"
    b:9
    Control + Tab 

#Tab Left
"xdotool key control+shift+Tab"
    b:8
    Control+Shift + Tab
```

The `xdotool key` command followed by a key combination will execute that key combination. You can try this in a new terminal with `xdotool key Alt+F4`. You'll notice that this closes the terminal window, just as if you'd pressed Alt+F4 on the keyboard.

The b:8 and b:9 were our button presses from earlier, you should change these depending on your results from `xev | grep button`

The Control + Tab seems to be a descriptor, and when changed seems to have no effect.

That's it! Save the file and make sure xbindkeys is set as a startup application in 'Startup Applications'. I hope this helped!

## A Word to the Wise

While this was a useful addition to firefox, there were some points where I liked having the 'back a page' and 'forward a page' options, especially in Files. 

I found that holding Control and clicking these buttons bought back their original functionality, though I'm not sure how stable this is...

## Optional Code

There is a GUI for xbindkeys that we've already installed above. by typing `xbindkeys-config` into the terminal, a GUI opens that allows you to edit the `.xbindkeysrc` file.
