---
title: "Contributing to Bitcoin - tips and tricks"
date: 2022-10-27T13:32:05+02:00
tags:
    - Bitcoin
    - Computer science
    - BDK
---

I've been working on Bitcoin-related OSS for a while now - long enough that I don't fear `git reflog` anymore.

As you might guess, there was a time when I used to feel scared just thinking about the whole you-publish-your-code-and-then-strangers-look-at-it thing - luckily for me, I had friends[^0] helping me out, encouraging me to venture out my comfort zone, reading my code before I would post it and, why not, helping me with merge conflicts.

There are many people out there who are smart and humble enough to contribute significantly to Bitcoin, but are not as lucky as I am, and feel quite lost - this is my way of trying to help. In this post will cover the fundamentals for starting to contribute to open source projects:
- [How to pick the right project](#pick-a-project)
- [How to start diving into the code](#diving-into-the-code)
- [How to review code](#reviewing-code)
- [How to write code](#writing-code)

I'm well aware that a blog post isn't as helpful as a IRL mentor, but hey, I'm doing what I can :)

<!--more-->

Don't expect this to be too structured, tho - it is just a collection of notes, random shower thoughts and code pro tips, resembling a stream of consciousness more than a book - there are huge holes here and there, I probably forgot to explain some important steps, I probably got lost in details every now and then.

Don't worry if some parts seem obscure to you - you can get back to them later. Some concepts circular reference each other, and you can't understand A without first understanding B, and you can't understand B without first understanding A, and you can't understand A without first understanding B, and you can't understand B without first understanding A, and oooooopsie, StackOverflowError.

Needless to say, 99% of the tips listed here come from the experiences I had on the projects I worked on - but they might not apply to the project you want to contribute to (The Black Swan, anyone?). Be humble, be ready to apologize if you do something wrong, and keep in mind that I'm 101% not responsible if you do something stupid :P

I'm sure you'll find something useful in this blog post, even if you're not a complete beginner anymore. Enjoy!

# Pick a project

Find a project that sounds interesting to you. Are you into electronics? Consider contributing to an hardware wallet implementation. Do you like working on mobile apps? Pick your favorite mobile wallet and start coding. Are you up for a challenge? Lightning Network will do the trick.

Some projects regularly onboard many new contributors, while others are developed by a small team and rarely get external help. If you're really new to coding, stick to large, popular projects, as you're going to have more people helping you out along the way.

Bitcoin Core is probably the most popular choice, as the project covers many aspect of Bitcoin, it's full of maintainers willing to help out newcomers, the documentation is impeccable and everyone loves bragging about contributing to Core, for some reason.

# Diving into the code

This is a really important phase which is often skipped - before rushing into writing the code, you need to take some time to explore the project, its goals and its limitations.

The exploration phase is all about, you guessed it, exploring. It should be unbounded - it can last minutes, hours or days. Play around without necessarily having a goal, or expecting *something* to happen: try all the different features, click on all the buttons, use all the possible commands, and see what happens. Some inspiration to get you started:
- What's the nature of the project? Which problem is it solving? Who is it for?
- Are you looking at a library, or an executable? Is it production ready?
- Is there a project documentation, or some official tutorial?
- Where do the devs communicate? Is there some public chat (Discord, Slack, IRC, etc.) you can join?

Clone, compile (if needed) and run the code (if possible) - this step might already be though. You can usually find some help in the project `README.md` - for example, [HWI](https://github.com/bitcoin-core/HWI/)'s `README.md` clearly states how to install and use `hwi`. [Bitcoin Core](https://github.com/bitcoin/bitcoin)'s, on the other hand, simply tells you to check out the `doc` directory, which contains a detailed explanation for every platform.

Some projects don't have any detailed documentation on how to build or run it - that usually means it's trivial to do so. For example, [BDK](https://github.com/bitcoindevkit/bdk) is built in the same way as every other Rust project: using `cargo build`. Yep, we could write that down in the `README.md`, but we assume that if you want to contribute to a Rust library, you've already used Rust at least once, and you know how to compile Rust code.

If you're exploring a library, consider building a small program using it, to gain familiarity with the API. It doesn't have to be something useful, or exceptionally complex, as it's just for education purposes. In the case of BDK, you might write 25 lines of to check the balance of a testnet descriptor.

Then, let's look into the code itself. How is it structured? Identify the different modules available and what function they serve. For example, if we look at BDK's directory structure:

```
bdk git:master  
❯ ls -la src
total 76
blockchain
database
descriptor
doctest.rs
error.rs
keys
lib.rs
psbt
testutils
types.rs
wallet
```

We can see that there are many different modules - let's try to guess what they do:

- `blockchain` - interacts with the blockchain, probably?
- `database` - mh, this looks like it's interacting with a db. It's probably used to save data?
- `descriptor` - ah, Bitcoin descriptors! I've read about them somewhere, but I can't remember what they do... *proceeds to google Bitcoin descriptor*
- `keys` - some kind of code for interacting with bitcoin keys? xpubs, WIFs, so on.
- `psbt` - mh, partially signed transactions... It probably contains some utils for handling PSBTs.
- `testutils` - oh, tests! I've found them!
- `wallet` - ah, this must be the code for the wallet itself. It probably uses all the other modules to build a wallet.

As you can see, we're not at all trying to understand perfectly what each piece of code does: we just want to have an intuition of what the different modules do. This will come in handy later.

![](../images/oss/magictool.png)

Last step of the exploration phase: the tests!

Normally, it is stated *somewhere* how to run tests - if not, just try running the *usual* tools for a language: `pytest`, `cargo test`, etc.

If you can't understand something, feel free to ask for help, but don't do it straight away - spend a bit of time investigating the problem, knowing that even if you can't get to the solution yourself, you'll still learn something along the way. Try to remember that the project maintainers are probably handling many other newcomers like you, and try not to be a burden to them.

![](../images/oss/harshbuttrue.jpg)

Warning: Many Bitcoin projects use IRC for communications, which is, and this is coming from someone born not-so-long-ago, no judgment here, I'm just trying to be honest, please don't be offended, a *weird* messaging platform. If you try to connect to https://libera.chat you'll see exactly what I mean.

I removed the weirdness by using Matrix bridged to IRC, which means that I get to speak with the people on the weird platform, while staying on a normal platform (I know, magical). You can register on https://matrix.org (the easy way) or host your own homeserver (the not so easy way). And it just works (until it breaks).

# Reviewing code

Reviewing code is the ~~art~~ act of reading someone else's code to check its correctness before merging it on the master branch. The code review step is crucial but it can be boring at times, or at least less fun than writing code yourself!
Nonetheless, you should help with reviewing open PRs - the more people carefully read the code, the lower the probability of a critical bugs slipping in. Even if you're new to the codebase, your review can be very helpful: noobs are more likely to challenge assumptions, are better at noticing when the documentation is lacking, and can come up with difficult-to-answer-but-extremely-insightful-questions (worth mentioning: not every question you ask is insightful).

Bitcoin Core guidelines ask contributors to review between 5 and 15 PRs for every PR they open - this number obviously depends on the project size, and on the number of PRs opened every day. There's no way to figure out the exact number for every project, but, as a rule of thumb: try to give more than you take. Review more PRs than you open.

But... how do you actually review code?

### Find something to review

Open the PRs page and find something to review. Avoid huge code refactoring, and stick to small-to-medium-size PRs or test changes/additions. Find some PRs that sound interesting for you - if you really don't like networking, maybe don't start with reviewing Bitcoin Core's P2P code.

### The review phase 

First, a warning: reviewing code takes time. Smaller PRs can take 15 minutes to review, while bigger PRs usually take hours. Obviously, you shouldn't spend 6 hours looking at the same piece of code: take a break once in a while (not too often!), and don't feel pressured to finish the review the same day you started it, or even to *finish* the review at all before publishing it: as long as you're saying that you just quickly skimmed through the code, it's fine to just post a couple of questions, and come back to the PR later.

Try to understand what's the goal of the PR - is it fixing a bug? Is it adding a feature? If an issue is linked, read it.

Fetch the code:

```
git fetch origin pull/PR_NUMBER/head && git checkout FETCH_HEAD
```

Run the tests, and, if possible, do some manual testing as well, either trying to reproduce the old bug (which should be gone by now!), or playing around with the new feature.
If you're reviewing a bug fix, you should also try to reproduce the bug on the master branch, to make sure that you're understanding clarly what was the problem.

Time to read the code!

I love reviewing commit by commit, if the author knows how to properly commit. And I despise reviewing commit by commit if the author doesn't! (No worries, I am eventually going to explain how to commit).

If it's a big enough project, there's a high chance that the PR author knows what he's doing, and reviewing commit by commit should be doable.

Read the code, and try to understand what each change does - first, on a high level: how is the code structure changing? Is the author adding new methods? New classes? New modules? Modifying the old ones? Those are not at all easy questions: take your time to understand how the code was, and how it is changing.

Secondly, review the PR on a lower level: how is each method implemented? What's the algorithm used? Can it be improved?

You should actively try to break the code. Either on the high level, identifying structures that can be simplified, or that can be wrongfully accessed from the outside; but also on the lower level, looking for bugs in the implementation and things that might go wrong. The latter is easier for a beginner, so maybe focus on that.

Lastly, check that tests have been added to the PR (it's rare that a PR doesn't need anynew ones!), and when there are tests make sure as much code as possible is covered.

While reading the code, [write comments](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/commenting-on-a-pull-request) every time you find something that can be improved. You can put "nit:" in front of your comment if it's not really relevant, and it's not blocking the merge. For example:

> nit: I would change the variable name from `to_address` to `address`, as it's more consistent with the rest of the code

> nit: Typo: it should be "platypus", not "platipus"

You should end the review by publishing your comments, and optionally putting a verdict, such as `tACK`, `NACK`, `utACK`, etc. - the lingo is explained here: https://github.com/bitcoin/bitcoin/issues/6100

Avoid putting a mere `ACK` if you're not confident that the PR code is correct - if you have any doubts, you can still put a partial `ACK`, explicitly stating which parts of the code don't fully reason with you. For example: https://github.com/bitcoindevkit/bdk/pull/614#pullrequestreview-1003451363

Remember: reviewing PRs is a skill that takes time to develop. Keep it up!

If you want to learn more (you should):
- https://github.com/glozow/bitcoin-notes/blob/master/review-checklist.md
- https://jonatack.github.io/articles/how-to-review-pull-requests-in-bitcoin-core
- https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests (this whole document shows how to use Github for PR reviewing)

# Writing code

The steps for writing code on a project are the following:

- Find something to do
- Code
- Fork the repo
- Push code to your fork
- Open PR
- Receive reviews, and update the code as needed
- When maintainers are satisfied, they will merge your code
- Success!
- (optional) [Brag on Twitter](https://twitter.com/danielabrozzoni/status/1531297847630606336?s=20&t=n3Z8jctpg9nxv6E9ois58Q)

Before starting to work, read the project's `CONTRIBUTING.md` file. It usually contains a lot of copy-pasted stuff telling you not to be an asshole, and some project-specific tips - where to ask for help, the review process, the release process, etc...

### Find something to work on

Look for the `good first issue` label - those issues are usually really easy, and a good way of getting to know the codebase.

Good first issues are usually relatively scarce in a project[^1] - be mindful of others, and try to avoid solving every good first issue by yourself! After you completed one or two of them, it's probably time to start working on something else. Pick up a low-priority issue and start coding :)

If you really have no clue of what to do, increasing the test coverage is always useful. [Here](https://github.com/bitcoindevkit/bdk/issues/699) I put some instructions for BDK contributors, but the steps are the same for every project: find a way to generate a code coverage report, find code snippets not covered by current tests, write new tests :)

### Git

https://rogerdudler.github.io/git-guide/

### Forking the repo

https://docs.github.com/en/get-started/quickstart/fork-a-repo

### Writing commit messages

https://cbea.ms/git-commit/

### Commit hygiene

A big problem everyone faces when using git is: how often should I commit? Should I put my 1000 changes in one huge commit? Should I create a lot of incremental, small, commits?

I googled far too much in search of the perfect article on commit hygiene, and couldn't find anything. [I thought for a moment that no one shared my ideas](https://twitter.com/danielabrozzoni/status/1560210064224526336?s=20&t=FHOwrG--ffkH3VKbyTVfcg), and felt quite lonely in the world. Then, I stumbled upon this: https://github.com/bitcoin/bitcoin/blob/master/CONTRIBUTING.md#committing-patches, and now I'm a happy human again.

More blog posts on the topic (they're nowhere near as perfect as the one linked above):

- https://blog.lanesawyer.dev/17501/git-hygiene
- https://medium.com/@martindeto/commit-hygiene-git-blame-and-squashing-commits-58d1f511ea83
- https://medium.com/transmute-techtalk/improve-your-commit-hygiene-with-git-add-patch-3b7dd9c117c4

### Signing commits

https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits

### Before pushing

Run a `git log`, and double check that the history is clean. All your commits should have a clear commit message and optionally a description. None of your commits should have "lol everything is broken" as a message.

Tip: if you're not a native English speaker, copy-paste the commit message/title in a spell checker - it will save you some embarrassment later (I once managed to misspell "whether" *twice* in the same commit message...)

For each commit, run a `git show`. Check that you haven't committed your grandma's pics by mistake. And check that you haven't left ugly trailing whitespaces around.          
Trailing whitespaces are the worst.    

![](../images/oss/whitespaces.png)

If you're introducing new features, make sure that they're well-tested. Think of all the edge cases, don't just test the normal conditions! Code coverage tools can help.
 
### Opening PR

https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request

### Handling reviews

You opened your first PR! Yay! It's just a matter of time before someone will politely tell you why your code sucks.

When that happens, don't be sad - even the experienced devs write broken code! Fix your code, and push again. Remember: [commit hygiene](#commit-hygiene)! Don't push a "fix things" commit on top of your PR - instead, rewrite history. More on that later.

Make sure to click "resolve conversation" after you, well, *resolved the conversation*.

An unresolved conversation looks like this:

![](../images/oss/not_resolved.png)

While a resolved one looks like this:

![](../images/oss/resolved.png)

As you can see, resolving helps keeping your PR clean :)

When everything is fixed, it's time for another round of reviews.

At this point, I don't really know if clicking the "request review" button is polite or not, so I just avoid it. I usually either comment something along the lines of:

> Thanks for the reviews, now it should be ok.

Or I just push, resolve conversations, and patiently wait for someone to get back to my PR.

### Rewriting history

Getting your code reviewed is ALL about rewriting history. It happens all the time:
- Someone tells you that a function is ugly
- Someone tells you that your commit message is ugly
- Someone tells you that, even tho your code is pretty cool, it's fragmented in 15 different commits that should be squashed
- Someone tells you that, even tho your commits are pretty cool, you should reorder them to make the history bisectable

This article is amazing, and explains everything you need to survive your first code review: https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History

If you've never interactive rebased before, https://git-rebase.io/ can be of help.

### Rebasing and fixing conflicts

So, your PR has been open for a long while, and someone commented "please rebase this" - probably because your changes conflict with master. This is easily solvable:

```
# This first commands needs to be done only the first time you rebase
git remote add upstream *link to the upstream repository*
git fetch upstream
git rebase upstream/master
```

And then fix the conflicts.

A tutorial on fixing the conflicts: https://docs.openstack.org/doc-contrib-guide/additional-git-workflow/rebase.html - this one uses slightly different commands for updating the branch, but the result is the same.

To avoid getting confused, I'd suggest you to avoid using `git merge` - fetch & rebase works flawlessly, and you won't risk opening a PR with ugly merge commits, which maintainers will for sure ask you to remove:

![](../images/oss/merge.png)

More on the difference between rebase and merge: https://stackoverflow.com/a/804156

### Please don't force push

You should **NEVER** `git push --force`, not even when you think it's really necessary - instead, `git push --force-with-lease`, which prevents you from overwriting other people's work.

More info: https://stackoverflow.com/a/52823955

# Don't forget to have fun

Go to conferences and meet people. [Join online seminars](https://learning.chaincode.com/#seminars). Join in-person BitDevs (if there isn't one in your city, organize one). [Join review clubs](https://bitcoincore.reviews). Find a way to make friends in the space and stay connected with them.

And just have fun.

[^0]: thanks frenzs *sending love*
[^1]: At the time of writing, BDK's [good first issues](https://github.com/bitcoindevkit/bdk/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) are not at all scarce, and I'm proud of this :)
