---
title: Dr. Plywood and Mr. Formaldehyde
slug: dr-plywood-and-mr-formaldehyde
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2021-09-30T18:47:55.000Z
tags: #Import 2022-12-26 21:35
draft: true
---

So, I'm not sure how the epiphany hit me, most likely I read something in the handful of articles that distract me throughout the day, but I started to wonder about the safety of the plywood I've been laser cutting in my Glowforge.

It was even a bit more worrying as I had been using Baltic Birch I purchased from Amazon. It was so much cheaper than the stuff from the Glowforge Shop. I'm pretty sure I'm not the only one who as been bypassing the GF Shop as this particular Birch I recently bought had 4.4 stars with 125 reviews. Anyways, being as Baltic Birch originates from around the Baltic Sea (hence, it's name) and therefore an imported product AND knowing many other countries outside of the EU have less than stellar safety guidelines, I started to look into whether this plywood might be killing me softly.

One of my main concerns was formaldehyde. As in the scandal that wracked Lumber Liquidators back in 2015 or the FEMA [trailers](https://en.wikipedia.org/wiki/FEMA_trailer). Many organizations have labeled [formaldehyde](https://www.cancer.gov/about-cancer/causes-prevention/risk/substances/formaldehyde/formaldehyde-fact-sheet) as a known carcinogen and [OSHA](https://www.osha.gov/OshDoc/data_General_Facts/formaldehyde-factsheet.pdf) has specific limits as to how much airborne formaldehyde employees should be exposed to in their workplace. One point for concerned. On the other hand, we are constantly exposed to formaldehyde by natural products such as fruits, meats and seafood, but at levels which are comparatively low compared to the OSHA limits.

So far, formaldehyde in plywood is not looking all that great. Especially at exposure levels mentioned by OSHA, the state of [California](https://www.cdph.ca.gov/Programs/CCDPHP/DEODC/OHB/HESIS/CDPH%20Document%20Library/formaldehyde.pdf), a [branch](https://www.ncbi.nlm.nih.gov/books/NBK217660/) of the National Institutes of Health, the [EPA](https://www.epa.gov/sites/production/files/2016-09/documents/formaldehyde.pdf) and host of other people and organizations, including an agency within the [CDC](https://www.atsdr.cdc.gov/mmg/mmg.asp?id=216&amp;tid=39). A great [summary](https://www.cancer.org/cancer/cancer-causes/formaldehyde.html) by the American Cancer Society is a good read if you want to know more.

At the same rate, I decided not to get my tighty whities in a bunch over all of this just yet. There was also a [study](https://www.ncbi.nlm.nih.gov/pubmed/28813411) that seemed to say that we actually produce around 50 grams of formaldehyde in our bodies (depending on weight) and that this in a way keeps us alive. I think, that study is pretty obfuscated. Also, knowing how stringent the US is about safety, the OSHA and other governmental limits are probably pretty conservative.

YMMV. Make your own assumptions.

Back to laser cutting. OK. So I need to measure how much formaldehyde is released in the air when I cut the Baltic Birch and then compare this to another plywood that I found which is supposed to be devoid of formaldehyde. Later, I will also compare the plywood Glowforge Shop sells. But! I need to do this without breaking the bank. At this point, I also feel I need to add the caveat that I am neither a scientist (well, I do have a degree in computer science, but I'm pretty sure that doesn't make me a scientist, per se) nor am I trained for detecting formaldehyde (yes, there are people who do that – of course). 

To do all of the testing, I found a doo-dad from somewhere that allowed me to do some metering that my unskilled mind could handle and do it without making the wallet too much lighter. And here it is:
![](https://scratchthecurious.com/content/images/2020/03/IMG_9851-1.jpg)Isn't she a beauty?
Soooo... the meter I bought is 1. OLD; 2. Not calibrated as the calibration standard is expired. If I can find some inexpensive calibration standard(s) (the yellow tube thing in the case) then I'll definitely be calibratin' this thing and updating the blog accordingly. Don't hold your breath though. I know, I know, I saw those formaldehyde meters on Amazon that go for like $20-50, but in my mind I'd rather have an industrial strength measuring tool that's old and out of date than those things. Maybe I'll buy one or even cobble something together using this [sensor](https://www.dfrobot.com/product-1574.html) and some Arduino gear and then compare the results.

The nitty gritty. Here are some results of using the meter (all the numbers are in PPM – Parts Per Million):
![](https://scratchthecurious.com/content/images/2020/03/IMG_9839.jpg)Measuring of some Baltic Birch about a week after laser cutting.![](https://scratchthecurious.com/content/images/2020/03/IMG_9842-1.jpg)Picked a job I knew would take a decent amount of time and had a lot to cut.![](https://scratchthecurious.com/content/images/2020/03/IMG_9841.jpg)Before running the job, I got a meter reading.![](https://scratchthecurious.com/content/images/2020/03/IMG_9844.jpg)Got another reading ~10 min into the job.![](https://scratchthecurious.com/content/images/2020/03/IMG_9846.jpg)Job is finished, that's 1.06 ppm.![](https://scratchthecurious.com/content/images/2020/03/IMG_9847.jpg)Around 1.5-2hrs after the job and opening the doors to the outside to air the room out.![](https://scratchthecurious.com/content/images/2020/03/IMG_9848.jpg)Same exact cut on a plywood using formaldehyde-free resins. Still reading a bit high for formaldehyde-free in my mind, but probably unavoidable as I AM burning wood...![](https://scratchthecurious.com/content/images/2020/03/IMG_9849.jpg)Another reading for the Baltic Birch close to an hour later. Pretty close to the reading of the formaldehyde-free wood right after laser cutting.
My takeaway from the testing so far. 

I'm thinking I'm pretty pleased with my meter. Most information out there suggests indoor readings on par with what I was getting. I didn't show various other objects I tested that were negligible in raising the meter readings, but maybe I'll include that in a later posting. I had actually tested my breath and got a reading of .13ppm – I know, I know, the jokes ensue, but I'm willing to be labeled Toxic Breath for the interest of what passes as science in my world. Hmm, I wonder if I get the same reading from the other end??

The Glowforge seemed to do a pretty good job of keeping me from being gassed by formaldehyde according to my sketchy meter. While working it's magic, I took several readings and they all hovered between .01 and .02 ppm. At the same rate, it would be nice if we could manually control the exhaust/fan so that we can have it run much longer after the job finishes or even all day if need be. Once you open that lid, all of the fumes are escaping to your face and the room.

In my mind, there are certainly questions as to the safety of cutting the Baltic Birch with any laser cutter and there probably should be the same questions made by others who use laser cutters for similar material (i.e. other plywoods). Despite the questions surrounding the meter, I would think that as it is measuring using electrochemical means that fade with age, my readings should actually be showing measurements that are LOWER than in reality.

Well, I won't be making any stuff for my kids using Baltic Birch for sure. Maybe you shouldn't either. 

Now that I have the formaldemeter, I'll be buying some other "formaldehyde-free" plywood and trying this again. The formaldehyde-free plywood I used here is SoyStrong from Lowes. Next up is another brand and some stuff from Glowforge directly!
