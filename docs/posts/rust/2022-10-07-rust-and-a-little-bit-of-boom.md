---
title: Rust and a little bit of BOOM
slug: rust-and-a-little-bit-of-boom
date_published: 2022-10-07T09:25:37.000Z
date_updated: 2023-04-01T01:03:29.000Z
tags: #Import 2022-12-26 21:35
---

As I have been a bit rusty with Rust, I decided to get back into the swing of things with Exercism. If you don't know, [Exercism](https://exercism.org), is where developers and programmers bring their gym bag for a bit of mental exercise. I've checked it out briefly before, but recently I'm all in and hitting the weights again.

Exercism challenges you with tasks that are rated for their level of difficulty and there are tracks for a [ton](https://exercism.org/tracks) of different skills and languages to partake. I all of the exercises pretty cool and even better, it's free (queue the [Sit n Sleep commercial](https://youtu.be/r1a4vXiPO0o?t=28))! 

There was one exercise in the Rust track in particular that caught my eye, Minesweeper. It's a pretty iconic game for the older generation (that's me) and was included with the Windows OS for a while. You can read more about the history of it [here](https://en.wikipedia.org/wiki/Minesweeper_(video_game)). After completing the exercise, I had the feeling I wanted to clean up my code a bit, but instead, I opted to make it messier with some UI.

After a brief, which is never brief for me, search, I came upon [Slint UI](https://en.wikipedia.org/wiki/Minesweeper_(video_game)) for Rust. Not sure about the naming. What did catch my eye was their marketing shtick of being "fast and easy." And if you know me and UI, I'm ALL about fast and easy... wink... wink... Immediate mode was also kind of interesting.

So! On to implementation. Oh, BTW, [here](https://gsuyemoto.github.io/rust-bombfield/) it is in all of it's WASM glory. Some quick thoughts on Slint:

- It IS pretty easy
- Performance seems pretty good. Haven't done any testing though.
- Building the game UI was pretty straightforward and simple
- Their online interpreter for previewing UI needs some work from my exp
- Debugging the .slint file was no bueno (I'm using VS Code)
- All in all a pretty nice exp
- Would be nice to be able to use .gif

Anyways, I changed my Minesweeper code from the exercise to be able to randomly generate a new bomb field for my game, renamed it to the clever name of Bomb Field and we were off!

Right now the game is still really basic. I didn't even bother to implement the game mechanics where if you click on an empty space then it expands until all the empty spaces are bordered with numbers (if you know what I'm talking about you just know, otherwise go check it out at some online place like [here](minesweeper.online)). I don't plan on investing more time in this version as I'm more interested in adding some nice gameplay animations and sound and I don't think Slint UI is a particularly good use case for these features. Maybe use a minimal game engine like [miniquad](https://crates.io/crates/miniquad)?

Overall, using Slint was a pretty cool experience and I'll be sure to use it for what it was really meant for, IoT. I do have some IoT projects in the repo and maybe I'll give them some fancy UI? Maybe finally realize my vision of a REAL cornhole (one word?) game with a nice digital score board and some nice graphics for every point?!? Some nice handheld Raspi games, ala [electronic football](https://www.youtube.com/watch?v=wy7KGxxeQ_I) anyone?

Ah, fast and easy. It's alluring indeed.
