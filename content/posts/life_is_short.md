---
title: Life is short, Bitcoin is forever (a kinda-transcript)
date: 2022-05-21T17:06:09+02:00
tags:
    - Bitcoin
    - BDK
    - Talk
---

Paralelni Polis organized a Pizza Day conference! It was short and sweet, less than 48 hours long, but a really nice occasion to meet with fellow bitcoiners and ~~get drunk~~ eat pizza!

I gave a pretty sad talk about death, but people seemed to enjoy it, for some reason. I decided to write a *kinda-transcript* around the slides, so I could share the sorrow with everyone who opened my blog, instead of limiting it to the Pizza Day participants. Pretty altruistic if you ask me.

<!--more-->

I'm not literally going to transcribe everything I said - I'm just going to add some comments to the less verbose slides. 

(Psss, if you prefer watching the recording, [here it is](../life_is_short_talk))

Well, let's get started.

------- 

*Audience claps, Daniela is a bit embarassed, Daniela starts talking*

![](../images/pizzaday2022_slides/life_is_short.jpg)

------- 

![](../images/pizzaday2022_slides/life_is_short_1.jpg)

Death exists - (almost?) all of us agree that death exists. We can't really agree on what death is, what it feels like, what happens next. But we kinda all agree - death exists.

Death happens when you least expect it. If you try to imagine your death, you'll probably imagine yourself dying of old age while sleeping. But death happens every fucking day, in every possible scenario. Your plane might crash. A drunk driver might hit you. You might fall and break your neck.

Death just happens.

As humans, we also tend to ignore that death exists. I don't know about you, but I don't think "oh, this could be my last meal" every time I cook dinner.
It would be scary to live while continuously thinking about the end of life.

--------

![](../images/pizzaday2022_slides/life_is_short_2.jpg)

There's this sentence - a pretty cool one. "Memento mori" - meaning "remember that you'll die". Stoics particularly appreciated it - they would get tattoos or keep objects in their pockets to remind themselves of such wisdom.

"Memento mori" is not a depressing message, as you might think - in fact, life is exciting *because* it's finite. Life is exciting because we have to decide how to allocate our time. Life is exciting because we can't do everything, and we have to choose what matters.

"Memento mori" means not forgetting that some things matter, and some don't - it's suggesting we stop being angry for no reason, stop chasing useless things, stop being trapped in a boring routine. "Memento mori" is asking us to live.

--------

![](../images/pizzaday2022_slides/life_is_short_3.jpg)

I hope you got that - it's important, for normal people, to realize that life as we know it is going to end.

I'm here to argue that for Bitcoiners it's even more important to remember death - as, on top of *being humans*, most of us also want their coin to go to *someone* when they die.

Thinking about what will happen to your family when you're gone is tough - but you should really do it. And you should do it now. Again, death is unexpected.

--------

![](../images/pizzaday2022_slides/life_is_short_4.jpg)

This is the transition slide, where we go from talking about a kinda sad topic to an exciting one.

...Cuz, everyone finds Bitcoin technicalities exciting, right?

This first section is about Bitcoin basics - the bare minimum you need to know to understand what I'm talking about.

--------

![](../images/pizzaday2022_slides/life_is_short_5.jpg)

![](../images/pizzaday2022_slides/life_is_short_6.jpg)

![](../images/pizzaday2022_slides/life_is_short_7.jpg)

![](../images/pizzaday2022_slides/life_is_short_8.jpg)

Transactions, inputs, outputs, locking and unlocking scripts - you probably all know what I'm talking about. If not, https://www.oreilly.com/library/view/mastering-bitcoin/9781491902639/ch05.html can help.

--------

![](../images/pizzaday2022_slides/life_is_short_9.jpg)

If you want to play around with policies and miniscript: https://bitcoindevkit.org/bdk-cli/playground/ or https://bitcoin.sipa.be/miniscript/

--------

![](../images/pizzaday2022_slides/life_is_short_10.jpg)

![](../images/pizzaday2022_slides/life_is_short_11.jpg)

I have to admit, I survived **years** without fully acknowledging the difference between transaction-level and output-level timelocks. If you don't get this last slide, don't worry too much about it.

If you're really curious: https://medium.com/summa-technology/bitcoins-time-locks-27e0c362d7a1

--------

![](../images/pizzaday2022_slides/life_is_short_12.jpg)

--------

![](../images/pizzaday2022_slides/life_is_short_13.jpg)

I know, I know, you can close the page if you're annoyed. But I really can't give you a solution that fits each one of you.

The best I can do is:
- making you aware that there is a problem (done already, *hopefully*)
- giving you some ideas that you could use
- let you figure out the rest

--------

![](../images/pizzaday2022_slides/life_is_short_14.jpg)

--------

![](../images/pizzaday2022_slides/life_is_short_15.jpg)

--------

![](../images/pizzaday2022_slides/life_is_short_16.jpg)

Someone from the audience told me Coldcard's way of splitting the secret into multiple parts is superior to SSS, but I really can't find any information on that online. ~~If you have an idea what he could have been referring to, please let me know!~~

`<edit>`

Many thanks to [@SomsenRuben](https://twitter.com/SomsenRuben) and [@JohnCantrell97](https://twitter.com/JohnCantrell97) who kindly told me that the tool is called https://seedxor.com/ :)

Ruben also showed me this nice trick for splitting a 24 words seed: https://gist.github.com/RubenSomsen/4c148e5fe1338269666718ad45345b42.

As this scheme is not really standard, you have to remember (or write down somewhere) that you actually used it, or recovering your funds might be impossible. This actually applies to most of the tricks listed here: you should always back up which scheme you used to save your funds! When you move your funds to exotic solutions, it's fairly easy to just *forget* what you did and lose everything.

`</edit>`

--------

![](../images/pizzaday2022_slides/life_is_short_17.jpg)

![](../images/pizzaday2022_slides/life_is_short_18.jpg)

![](../images/pizzaday2022_slides/life_is_short_19.jpg)

![](../images/pizzaday2022_slides/life_is_short_20.jpg)

![](../images/pizzaday2022_slides/life_is_short_21.jpg)

![](../images/pizzaday2022_slides/life_is_short_22.jpg)

Again, this scheme is **full** of cons, and you shouldn't use it as-is - but, after discussing it with people, I came to the conclusion that with a bit of work it can become a really nice protocol. I'm just saying, don't disregard it just because it doesn't look great.

---------


![](../images/pizzaday2022_slides/life_is_short_23.jpg)

![](../images/pizzaday2022_slides/life_is_short_24.jpg)

![](../images/pizzaday2022_slides/life_is_short_25.jpg)

Notice that all the previous methods (1-4) didn't require you to move all your coins to a new mnemonic. This one, instead, requires moving all your funds to new addresses generated using this weird-looking policy.

A member of the audience made me realize there's no need to have the `and` clause in the last slide - I can just put the `after` clause in the `Treshold` and increase by one the number of sub-policies needed:

![](../images/pizzaday2022_slides/policy_fix.png)

(Also, to all the miniscript geniuses that are reading this: yep, I should have used `older` instead of `after`. Good catch!)

---------

![](../images/pizzaday2022_slides/life_is_short_26.jpg)

![](../images/pizzaday2022_slides/life_is_short_27.jpg)

--------

![](../images/pizzaday2022_slides/life_is_short_28.jpg)

![](../images/pizzaday2022_slides/life_is_short_29.jpg)

![](../images/pizzaday2022_slides/life_is_short_30.jpg)

![](../images/pizzaday2022_slides/life_is_short_31.jpg)

![](../images/pizzaday2022_slides/life_is_short_32.jpg)

![](../images/pizzaday2022_slides/life_is_short_33.jpg)
