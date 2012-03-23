---
layout: post
title: Story of a Notification Widget
excerpt: Experiments with UX
---

As a [GSoC](http://en.wikipedia.org/wiki/Google_Summer_of_Code) qualification task for VideoLAN/VLC, I thought to create something that I wanted in VLC, a small controller and media notification widget. This widget can be used as a controller (play, pause, back, forward, shuffle, loop), or to display meta data of the playing media. A sleek and elegant UI element now, started as a dull SVG widget. So, I made a prototype widget as my GSoC qualification task for VideoLAN/VLC.

<p style="text-align: center;"><img src="/images/videolan/vmm.png" ><br>Prototype I</p>

Though I was not selected for GSoC 2009, it was a learning experience for me to get to know the community and how things worked. So, I continued my work on this widget because I liked the idea and had plenty of time. When I showed the screenshot of my prototype to some people on Videolan's IRC channel on *freenode* I got nice responses;"good idea" "nice widget" "seriously good work" "kick-ass widget". I know GUI is not something most hardcore hackers would like to invest their time in, but anyway I went ahead with my GUI/UX development. Soon, I accomodated album art and redesigned the SVGs using Inkscape, in the 2nd prototype.

<p style="text-align: center;"><img src="/images/videolan/vmm1.png" ><br>Prototype II</p>

Until Prototype II, the code simply implemented the GUI, so the next step was to map UI events (mouse clicks and movements) and media playback interfaces. I hacked and grep-ed through the source to figure out the correct functions and after about an hour, the widget was fully working! The next few features I implemented were volume control, playback controller and a lock button to lock the widget from hiding. The widget would auto-hid itself after 5 seconds. The sliders were customised using CSS and SVG. I was suggested to remove CSS, so I'm trying to figure out a way to do that.

<p style="text-align: center;"><img src="/images/videolan/vmm2.png" ><br>Prototype III</p>

Finally, I did some color fixes and decreased transparency of the widget to give it a cool transparency effect.

<p style="text-align: center;"><img src="/images/videolan/vmm3.png" ><br>Prototype IV</p>

Then, I redesigned the lock-unlock SVGs and added 'drag n drop files to play' feature by connecting the drop event on the widget to the MainInterface. The code, GUI design, CSS and SVGs constitutes my original work. *j-b* helped during the course in reviewing my patches and my special thanks to him for all his help.

<p style="text-align: center;"><img src="/images/videolan/vmm4.png" ><br>VLC MiniMode</p>

I implemented a CopyLeft, a concept by RMS; so when you do a right click on the widget it will shows an "About Page" that changes Title, Artist and Album QLabels to one shown in the image shown below.

<p style="text-align: center;"><img src="/images/videolan/vmm5.png" ><br>VLC MiniMode - "Right Click About"</p>

After some discussion on videolan's irc channel, I was told that the widget may be used in place of the taskbar icon menu. So, may be in the future, when you right-click the VLC taskbar icon you'll see this floating widget. I cleaned a lot of code before re-sending another patch to the VLC-devel list; it was never entertained (or even considered to be commited) but I learned a lot from the experience. The patches can be searched and downloaded from VideoLAN's mailing list archives.

Meanwhile, I also made a *skins2* theme for VLC, "Dark Pepper", based on the same theme I designed for the MiniMode Widget. Download it from [here](http://www1.videolan.org/vlc/download-skins2-go.php?url=Dark%20Pepper.vlt) or [here](/files/old/dark-pepper.vlt). A screenshot of the same is below:

<p style="text-align: center;"><img src="/images/videolan/dark-pepper.png" ><br>Dark Pepper Skins2 theme for VLC</p>
