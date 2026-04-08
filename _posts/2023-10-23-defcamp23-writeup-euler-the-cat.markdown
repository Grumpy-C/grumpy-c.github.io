---
layout: post
title:  "DefCamp CTF 2023 writeup - euler-the-cat"
date:   2023-10-23 15:00:00 +1200
modified_date: 2026-04-08 23:00:00 +1200
categories: writeup
tags: ctf writeup defcamp23
---
(copied from my [CTFtime writeup](https://ctftime.org/writeup/38066))
## Task:
{% highlight md %}
Author: Volf

While recording a secret message for the National Day, our dear friend Alex forgot to close the door to his studio. Euler, his cat, while wandering through the studio walked from right to left on the keyboard and applied some unknown effects to the audio. Please help him reverse the transformations.

This challenge is proudly developed by Electron (ETTI).
{% endhighlight %}

## Solution:
When you listen to the file normally, you will hear "The secret message is..." for the first two seconds, before hearing distorted sounds. Upon opening the file up in Audacity and looking at its spectrogram, you will see that the distorted part has been reflected vertically.

If you switch the spectrogram to the Linear option, it becomes visible that the distorted part is reflected along the 10000Hz mark.

![Spectrogram]({{ "/assets/images/defcamp23/spectrogram.png" | relative_url }})

The non-distorted section looks very similar to the upper half of the distorted section. So, you remove the bottom half by using the Spectral Delete tool. Now, you have to move the upper section down by 10000Hz by using an external plugin, Frequency Shifter. The audio now sounds a lot more like talking, but it's still incomprehensible. To finally get the message, you simply have to reverse the section.

This is the following message now:

"The secret message is... the queen must die."

## Flag:
`{the queen must die}`

## Resources:
[https://www.audacityteam.org/](https://www.audacityteam.org/) - Audacity

[https://forum.audacityteam.org/t/frequency-shifter/66030](https://forum.audacityteam.org/t/frequency-shifter/66030) - Frequency shifter plugin