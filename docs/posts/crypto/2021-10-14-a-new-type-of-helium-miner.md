---
title: A New Type of Helium Miner!
slug: a-new-type-of-helium-miner
date_published: 2021-10-15T02:24:43.000Z
date_updated: 2023-04-01T01:04:35.000Z
tags: helium, #Import 2022-12-26 21:35
draft: true
---

OK, not new, but it's the newest type of Helium Hotspot to earn $HNT! The Helium Hotspot that I speak of is the Data Only Hotspot and it's actually been around since [Milestone 1](https://docs.helium.com/mine-hnt/light-hotspots/), but until today was not allowed to earn HNT from data transfer. It was just announced on the Helium [blog](https://blog.helium.com/announcing-mining-for-data-only-hotspots-ac2b2924ca99) yesterday, that Milestone 2 is complete and with that comes the ability for Data Only Hotspots to join the fray and earn some HNT!

Quick quiz. How many types of Helium Hotspots are there?

All of the mining on the Helium network has been performed by the workhorse that is the Full Hotspot, now that we have the Data Only Hotspot there is still one other Helium Hotspot in the works.

You got it, it's the Light Hotspot. So, to answer the quiz, there are 3 types of Helium Hotspots. You can check out the official documentation for all three here in the Helium [docs](https://docs.helium.com/mine-hnt).

What's the diff? Well, here's a very quick breakdown:
TypeEarningsCan you get one?FullPoC & DataGood luckLightPoC & DataMaybe, but you can't earn HNT until Q1 2022 anywaysDataDataYes! Earn a fraction of a Full/Light now!![Strong guy lifting a gigantic barbell of the Helium blockchain](__GHOST_URL__/content/images/2021/10/full-hotspot.jpg)Helium Full Hotspots do all of the heavy lifting with an entire copy of the Helium blockchain.

What's next? Well, Milestone 3 will provide access for the Light Hotspots, making the triumvirate complete. But ...

Milestone 3 will also bring about a pretty big change where all Full Hotspots will be converted to Light Hotspots. Huh? Basically, the Full Hotspots keep a copy of the entire Helium blockchain and must sync any new changes directly on the device. When Milestone 3 rolls around, this compute intensive work will be moved to the cloud in the form of the validators, just like a Light Hotspot.

Oh, and for you Rustaceans, there is a mention about the code for PoC compatibility to be changed from Erlang to Rust. I'm not entirely sure what this means exactly as there is not much details given in the article, but I will be diving into the Github repo to check it out. Anyways, it's pretty cool as the more code written in Rust the better in my book (I've been working with [Rust](https://www.rust-lang.org/) for about a year now and love it!). Go [here](https://github.com/helium/gateway-rs/tree/v1.0.0-alpha.19) to check out the code on Github yourself!

Milestone 2 & 3 seem like they will be completed in [Q1 of 2022](https://docs.helium.com/mine-hnt/light-hotspots/#milestone-3-introduction-of-light-hotspots).

I'm thinking you can put a Data Only Hotspot together yourself? Someone correct me if I'm wrong, but it seems the only thing stopping you from building the Full/Light Hotspots is the fact that you need the secret keys from Helium, whereas the Data Only Hotspots don't need these?

[Here](https://docs.helium.com/mine-hnt/data-only-hotspots) is Helium's documentation on how to get a Data Only Hotspot up and running in no time. 
