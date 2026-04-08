---
layout: post
title:  "IrisCTF 2025 writeup - Inferno Barrier"
date:   2025-01-06 12:00:00 +1200
modified_date: 2026-04-08 23:30:00 +1200
categories: writeup
tags: ctf writeup irisctf25
---
(copied from my [HackMD post](https://hackmd.io/ZFgbHoHiRjSoTKL-KWNlEg?view))
## Task:
{% highlight md %}
Sees a firewall policy.

"Whoa. This is worthless!"
{% endhighlight %}
## Solution: 
We have to receive and send packets in a network simulator. We can receive the first packet by turning promiscuous mode on with `prom on` and then running the `recv` command. This gives the following text message once decoded from Base64:
{% highlight md %}
Announcement: error: no announcement configured.
Run 'select (generic|date|time|flag)' to configure.
{% endhighlight %}

To send a packet back with the message `select flag` we need to swap around the ports and IP values, and then change the length in the packet. For example, if we have the packet in hex:
{% highlight md %}
45 00 {00 81} 00 01 00 00 40 11 F7 0C [C0 A8 01 0A] [C0 A8 01 04] (11 C1) (28 67) {00 6D} 9C D4 41 6E 6E 6F 75 6E 63 65 6D 65 6E 74 3A 20 65 72 72 6F 72 3A 20 6E 6F 20 61 6E 6E 6F 75 6E 63 65 6D 65 6E 74 20 63 6F 6E 66 69 67 75 72 65 64 2E 0A 52 75 6E 20 27 73 65 6C 65 63 74 20 28 67 65 6E 65 72 69 63 7C 64 61 74 65 7C 74 69 6D 65 7C 66 6C 61 67 29 27 20 74 6F 20 63 6F 6E 66 69 67 75 72 65 2E 0A
{% endhighlight %}
{} - Length values
[] - IPs
() - Ports

We can construct a new packet (as hex):
{% highlight md %}
45 00 00 27 00 01 00 00 40 11 00 00 C0 A8 01 04 C0 A8 01 0A 28 67 11 C1 00 13 00 00 73 65 6C 65 63 74 20 66 6C 61 67
{% endhighlight %}

In Base64:
{% highlight md %}
RQAAJwABAABAEQAAwKgBBMCoAQooZxHBABMAAHNlbGVjdCBmbGFn
{% endhighlight %}

Exit promiscuous mode with `prom off` and run `emit` with the new Base64 encoded packet, then go back into promiscuous mode with `prom on` and run `recv`, and you will get this:
{% highlight md %}
RQAAYQABAABAEfcswKgBCsCoAQQRwShnAE0Ys0Fubm91bmNlbWVudDogaXJpc2N0Znt1ZHBfMXBfc3AwMGZpbmdfaXNfdHIxdmlhbF9idXRfdW4xZGlyM2N0MTBuYWx9Cg==
{% endhighlight %}
Just decode this, and you will get the flag.

P.S: A lot of the swapping might not be needed? This solution worked for me, at least. Also I nulled out the checksum parts, which somehow worked too?

## Flag: 
`irisctf{udp_1p_sp00fing_is_tr1vial_but_un1dir3ct10nal}`

## Resources:
[https://hpd.gasmi.net/](https://hpd.gasmi.net/) (Packet analysis tool)