---
layout: post
title:      "Just Keep Swimming"
date:       2017-11-07 01:52:39 +0000
permalink:  just_keep_swimming
---


I got to round three on Regex Golf. 

This is not something to be proud of. But there is a point when it really becomes necessary to just let go and move on even when that seems counterintuitive. I assumed (naïvely) that as there were very few lessons in Regex, that it might just be some little snippet that *might* be visited later. Maybe not. 

Imagine my chagrin upon reaching OO Counting Sentences lab. There I was, breezing through (for a rare change), thinking that why yes, ternary operators are quite nifty. Oh hey, even handier is seeing if self.ends_with?("insert your character here")  Does self end with a period? No problem. Does it have an exclamation point? Sure, why not! Is there a question mark? Hold on a moment, let me go check. 

Then we reach the final question, which is usually the hardest one and usually the one that leaves me feeling as if I'm banging my head against a brick wall.  

After some googling (like you do), searching for "using multiple delimiters with .split in Ruby", old ye internet gods were pointing in one glaringly obviously direction. But really guys... sometimes...

<iframe src="https://giphy.com/embed/PtQrzJUJ7Q9d6" width="480" height="203" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/the-sandlot-youre-killing-me-smalls-PtQrzJUJ7Q9d6">via GIPHY</a></p>


Stack overflow tells me that the best way to split using multiple characters is using some Regex Code, because why would you use anything else? You know what mysterious smug coders on the internet, sometimes when you think you're helping, you're not helping those of us that feel like dumbasses. Which is constantly. 

It's not a question of finding the most concise answer, sometimes it's just a question of finding an answer that not only works, but makes sense when I want to go back in the future (like I always do) to see what the heck I did to solve the problem. 

I don't know about you, but 
```
(/[.!?]/)
```

sure as heck looks a lot more rational than 

```
(/\.|\?|\!/)

```

or this

```
(/[.|?|!][\s|$]/)

```

Or any other possible solution that one can find in a simple google search when looking for inspiration. Putting those bad boys into an array seems clearer to me. But run that through the IDE, it's not working because that doesn't account for possible multiple occurrences of characters. 

And isn't there something, some character that counts for one or more of these occurrences? Where is Mr. Kleene when you need him? The Kleene Star, my new best friend, because adding a handy-dandy little "+" on the outside of your array shall account for multiple's and split self at all the right possible places. So the code becomes:

 ```
 def count_sentences
    self.split(/[.!?]+/).count
  end
end

```

Now we're in business. No error messages. Huzzah! 

Question is, will I remember this tomorrow? 

<iframe src="https://giphy.com/embed/720g7C1jz13wI" width="480" height="333" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/harry-potter-idk-shrug-720g7C1jz13wI">via GIPHY</a></p>


