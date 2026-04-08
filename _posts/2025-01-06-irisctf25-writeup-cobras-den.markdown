---
layout: post
title:  "IrisCTF 2025 writeup - Cobra's Den"
date:   2025-01-06 12:00:00 +1200
modified_date: 2026-04-08 23:30:00 +1200
categories: writeup
tags: ctf writeup irisctf25
---
(copied from my [HackMD post](https://hackmd.io/nmKTlu2AS--e5egZC_TtEw))
## Task:
{% highlight md %}
You have entered the Cobra's Den, can you find a way to escape?
{% endhighlight %}
## Solution: 

To get the flag, we need to open a file named "flag" in the current directory.

We are restricted by:
- Only being allowed to use certain "filtered" built-in functions
- Can only use the characters in the string `<ph[(cobras.den)]+~`
- Only can use one period symbol
- Character count has to be less than or equal to 1115

Some functions we can use under these constraints are abs(), hash(), open(), chr(), ord(), repr().
Now, we need to make an instruction that simplifies to `open("flag").read()`. Because we cannot use the characters to make the string "flag", we'll use `chr()`  to represent letters, making our new instruction `open(chr(102)+chr(108)+chr(97)+chr(103))`. How do we make numbers if we can't type any?

There are many different ways you could approach this, but the way I figured it out was when you provide an empty tuple to `hash()`, it returns a number output. This is too big to be useful, so we turn it into -1 with `hash(())+~hash(())`. Now, we can start crafting a string.

We can make more numbers by using the binary NOT operator `~`, the absolute value function `abs()` , and the left shift operator `<<` like so:
{% highlight py %}
print(abs(-1)) # 1
print(abs(~abs(-1))) # 2
print(abs(~abs(~abs(-1)))) # 3
print(abs(~abs(~abs(~abs(-1))))) # 4
# ...
print(abs(~abs(~abs(~abs(~abs(~abs(~abs(-1)))))))) # 7
print(abs(~abs(-1))<<abs(~abs(-1))) # 8
{% endhighlight %}

I wanted to make it small enough for the length restriction, so I had to limit how many times I used the -1 equivalent.

The way I constructed the `chr()` numbers from this was to use many left bit shifts and adding/subtracting values from that. So, here is the new instruction:
{% highlight py %}
open(chr((1<<7)+~(1<<5)+7)+chr((1<<7)+~(5<<2)+1)+chr((1<<7)+~(1<<5)+2)+chr((1<<7)+~(1<<5)+8)).read()
{% endhighlight %}

Going up the substitutions, the final payload is only 905 characters! Sending it returns the flag.

This is what it looks like:
{% highlight py %}
open(chr(((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))))+~((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))+(abs(~abs(~abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))))+chr(((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))))+~((abs(~abs(~abs(~abs(~abs(hash(())+~hash(())))))))<<(abs(~abs(hash(())+~hash(())))))+(abs(hash(())+~hash(()))))+chr(((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))))+~((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))+(abs(~abs(hash(())+~hash(())))))+chr(((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))))+~((abs(hash(())+~hash(())))<<(abs(~abs(~abs(~abs(~abs(hash(())+~hash(()))))))))+(abs(~abs(hash(())+~hash(())))<<abs(~abs(hash(())+~hash(())))))).read()
{% endhighlight %}

## Flag: 
`irisctf{pyth0n_has_s(+([]<[]))me_whacky_sh(+([]<[[]]))t}`