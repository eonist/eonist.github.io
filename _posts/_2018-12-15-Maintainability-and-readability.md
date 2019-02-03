My notes on writing maintainable and readable swift code<!--more-->

### Speed
Although you’ll get slightly faster build time by merging all extensions into one class, at the same time you sacrifice code readability and ease of maintenance.

### Instance vs value
Using instance variables will increase your compilation time. In the end,

### Focus on comprehension from a cognitive perspective
Scrolling is bad for the brain -> cheetah -> BRAINS ARE GREATE WITH spatial thinking. scanning is cognitively expensive. Less info creates focus. And you don't have to look at unrelated data that you have to actively filter out. Build time wont suffer much from many files. Hierarchies are easy to reason about because your slice of what you are working on is unfolded, and your never working on everything at once.
