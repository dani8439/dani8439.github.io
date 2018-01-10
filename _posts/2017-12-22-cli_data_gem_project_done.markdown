---
layout: post
title:      "CLI Data Gem Project Done!"
date:       2017-12-22 20:28:46 +0000
permalink:  cli_data_gem_project_done
---


Today is a happy day, though, I'm not entirely so certain *how* happy. 

Amidst the chaos of trying to finish up the project, I found myself on slack in the learn channel. A very kind student, gbzhang likened the project as "the first time Learn really pushes you out into the ocean alone, so it's a lot to take in," and woo boy, was she right. 

My initial intention with the project was to create a Ruby Gem that scraped the best deals of Lego Sets from Brickset. I was highly ambitious. The original idea was for the user to be welcomed to LEGO Deals, and to then be offered a choice of five vendors, as those are the websites that Brickset itself collects information from. From there, the user would go one level deeper, and be offered a list of some 10-15 deals presently available at that website, and then ask the user which particular deal they wanted to investigate. I thought to myself, "hey, if I can get that working, then maybe I might even be able to add a third level and ask the user which country they wanted to list deals from too!"

Oh hell was I wrong. 

There was one enormous issue, one that was entirely out of my control. It took me 2 1/2 weeks - 2 1/2 weeks filled with family emergencies, and days where it was impossible to code because I couldn't be near a computer until 10 o'clock at night. On those days I found myself opening up the IDE and seeing if I could push the project just a few inches forward, but sometimes that didn't always happen. 

The two most difficult technical aspects of this project was webscraping the data (as brickset is full of tables), and attempting to figure out how to arrange the classes *if* I was going to go so far as list deals through the vendor. I understand that a country would have many deals through vendor, but I'm not quite clear on the relationship of vendor to deals. What is abundantly clear is that I am not entirely solid on the topic of OO, not to mention my scruffy skills with CSS. I know that I need to work more on these, for if I had a better understanding of scraping using CSS selectors, I never would have run into the first major wall with my gem. 

Building out the CLI seemed fairly straightforward thanks to the Walkthrough Video's that Flatiron has offered to us. I cannot count how many times I watched them in order to understand how to properly build an interface, but even then I found myself raising questions while watching Avi in the videos, or when watching Corinna build her Star Wars API gem in the study group sessions. I can say that I certainly feel better about interacting with the learn community, as up until now I have not gone to any previous study sessions, thinking that I would be able to figure out enough on my own, and just suffer in silence. But they are an excellent resource, and one that I definitely won't shy away from in the future, as I only have stuff to gain from watching them. 

The major difficulty I found with scraping, was that every time I scraped the data from brickset, I got back an empty array. 

I'll admit, I was stuck on getting back an empty array for several days, fiddling here and there to change certain pieces to get any information, and then I did something bad. I went back to the beginning and started to build another ruby gem from scratch, this time scraping information of the best Pizza places in NYC. If anything, going back to the beginning was good practice, as this time I was able to get all the way to the point of filling my array of Restaurant Instances with all the information I needed. Only, it was at that point that it occurred to me that such a gem really wasn't fullfilling the requirements of the project, as the website I was using was static. The list never updated. What I learned through that build, was how to pull more information from Brickset, so this time, instead of getting an empty array, I was getting a giant array of one or two objects, rather than 25. But hooray for small victories, because at least I could read the information I wanted all shoved into that object, I just had to figure out how on earth to get everything separated into individual objects. 

What followed was a lot of frustration. And then, horror of horrors, I tried to start the gem project from scratch a third time, this time pulling articles from a particular website that would update throughout the day. Again, I ran into the same wall with the final gem, of being able to only get either an empty array of objects, or only two giant ones.

I made an appointment through Learn with a 1:1 coach, and I cannot say how much Enoch helped me with working out the bugs in my project. I knew that the mistake in my scraping had to do with the CSS selectors, but I just couldn't figure out where. In the end, it was only a few little characters, and suddenly boom everything was working. The satisfaction of seeing that last night was amazing. And even better, it became clear that once Enoch helped me to get that working, I was within spitting distance of finishing the project. There was a little clean up to be done in the CLI interface, such as moving when to instantiate the list of today's deals from a separate scraper method the call method so that the list is only initialized once, and the scraper object doesn't have to continually scrape the list again and again, nor risk the chance of duplicates, or the information changing.

Enoch was extraordinarily helpful, and patient, and I definitely feel better about going to the instructors with questions. Despite however lost, or stupid I might feel in any of this, no one is going to say that you're a moron and you shouldn't be coding. There's nothing to do but learn. 

All in all, I fell alright with where the gem is presently, knowing full well that bugs will have to be fixed, and things I don't even realize have to be better written. I would love to be able to figure out how to make it go at least one level deeper, and I'm hoping that after the assessment, I'll be able to work on doing that the proper way, rather than getting a group of error messages that makes me want to throw the computer across the room, throw my hands in the air and scream that I'm not good at this. 

Today is a good day.  I think I'll be okay with where everything is for now. 

