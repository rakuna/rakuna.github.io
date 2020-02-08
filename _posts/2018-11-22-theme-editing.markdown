---
layout: post
title: Editing the theme
date: '2018-11-22 13:29:04'
tags:
- ghost-tag
- theme
---

Ghost comes with a nice default theme called [Casper](https://demo.ghost.io/) and also allows custom [themes](https://demo.ghost.io/themes/).
I like the fundamentals of Casper. I would also like to make minor edits/customisations from time to time, which is not possible with the default Casper version.

# Goal
I'd like to:
1) Edit the theme
2) Incorporate new Casper features as they're released (which demonstrate new Ghost features)
3) Avoid rework

# Strategy / Options
There are a few ways that this can be accomplished:
* Take a **static** copy of Casper and make the edits I want. Ignoring future updates **(achieves 2/3 goals)**
* Take a **static** copy of Casper and make the edits I want. Reapply the changes again with all future updates **(achieves 2/3 goals)**
* Take a **dynamic** copy of Casper by forking (branching) the Casper [github repository](https://github.com/TryGhost/Casper) and make the edits I want. Merging all future Casper updates with my edits **(achieves all goals)**



# Implementation
Since the git forking option achieves all my goals, this is what will be implemented.
1. Login to, *or sign up for*, a **github** account
1. Fork the Casper [repository](https://github.com/TryGhost/Casper)
1. Clone (copy) the forked repository to a local machine
1. Setup a [Ghost local install](https://docs.ghost.org/install/local/)
1. Make the edits desired
1. Commit all the changes made to your (forked) repository
1. Zip the theme files
1. Upload the theme to my Ghost install

# Closing thoughts
*What should I call my theme?* **Libre Casper** (for a bit of fun and to pay homage to the original).
*What licence should I apply to my theme?* **[MIT license](https://choosealicense.com/licenses/mit/)** (I'll copy the [Casper license](https://github.com/TryGhost/Casper/blob/master/LICENSE)).
