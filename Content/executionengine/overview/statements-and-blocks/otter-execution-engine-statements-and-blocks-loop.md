---
title: "Loop Blocks"
order: 8
---

Looping over the same series of statements can be useful for both visualization and removing repetitive statements in a plan. That's where the Loop Block comes in.

Loop Blocks run the set of statements in the block while enumerating over a list. The List can be a variable, a function, or a literal expression. 

For example, we can ensure a directory exists for each service in a variable called `@ServiceNames`. This variable could be defined elsewhere in our plan, or externally as a configuration variable.

**Visual Mode**
![loop-block.png](/resources/docs/loop-block%281%29.png){height="" width=""}

**OtterScript**
![loop-text.png](/resources/docs/loop-text%281%29.png){height="" width=""}

### Looping and Numbers 

With the built-in `@Range` function, you can loop over numbers just as easily as you might with a c-style "for" loop.

**Visual Mode**
![loop-range-block.png](/resources/docs/loop-range-block%281%29.png){height="" width=""}

**OtterScript**
![loop-range-text.png](/resources/docs/loop-range-text%281%29.png){height="" width=""}