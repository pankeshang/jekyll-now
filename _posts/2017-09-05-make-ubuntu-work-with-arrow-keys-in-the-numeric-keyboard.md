---
layout: post
title:  "Make Ubuntu work with Arror keys in the numeric keyboard"
date:   2017-09-05 10:50:00 +0800
category: os
tags: 
  - Ubuntu
  - keyboard
---

I have got an ASUS UX510U for work for some time. It is good and cheap and steady. But I do have some problem with it's keyboard layout. 

![Keyboard layout]({{ site.url }}/images/asus-ux510-img-keyboard-backlight.png)

source: https://www.asus.com/sg/Commercial-Laptops/ASUS-ZenBook-UX510UX/

The biggest problem of this keyboard layout, despiting the right arrow key invading the numeric keyboard and the lack of 
guidance keyboard bump for the arror keys (trust me, i got a lot of unintented 0s because of this),  is the missing of the "end" key.


And to what makes it worse, I am running Ubuntu 16.04, and the "end" key on the numeric keyboard is functioning very weird.

One of the sympton would be, [shift] + [end] is not selecting the characters from where the cursor used to be to the end of the line. 
It just moves the cursor to the end of the line.

This is a disaster.

Until just now I found this article 

https://askubuntu.com/questions/57079/xubuntu-make-shiftnumpad-work-like-windows

turned out, this is a configuraton issue.

goto `/etc/default/keyboard`, change this line:

```
XKBOPTIONS=""
```

to

```
XKBOPTIONS="numpad:microsoft"
```

and then run `sudo dpkg-reconfigure keyboard-configuration`


Voila, problem solved.

