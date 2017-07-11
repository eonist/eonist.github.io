### Jekyll 
I don't want to be forced to use front-mater if its not necessary, since the title and date is already apart of the filename<!--more--> , Keeping the same data in 2 different places feels redundant and defeats the whole ethos about ==keeping content and technology separated==. 

I also use a html comment to mark the post-excerpt-text. This was not straight forward to implement but my intuition led me to a solution of using the escaped string of the html comment to remove the tag in the output of the text. I added the solution to a couple of the [Jekyll] issue tracking pages on github. So other people can use the same solution. I also set the excerpt tag to work globally. Instead of setting it in every front-mater in every post. I feel the front-mater should only be used in edge cases. ==Most of the extra information should be derived contextually from the content==. 

A great "feature" if you want to draft post but not publish straight away, is to name the .md file wrongly. 

### Pixyll
[Pixyll] is the best theme I've found, it has 👁[Google analytics]👁, [Formspring] integration, Disqus integration, its animated, it has a pretty clean look although the CSS framework can be tricky to get an understanding of. Its also responsive so it works well with iPads and iPhones etc. 

### GitSync
I use[^1] [GitSync] on my macbook to automate the entire Git workflow, just type and let it stream on to the web. Like magic. Paired with [Atom.io] which has quick find and other smart editing features, the entire experience becomes more continuous, as you can quickly edit markdown files on the fly, ==articles becomes a living thing rather than collecting dust==, and you can use the articles in your work as a reference point, more like notes, you can review them and continuously add new findings etc. The technology is working for you, your not working for the technology. Just Spend time on writing and coding and let everything take care of it self. Oh and [textexpander] takes care of  ==Highlighting==, _italicizing_, **emphasizing**, ~~striking~~ and [linking](www.stylekit.org)

### Tag Cloud
I was thinking about including a tag cloud section, I already coded it in fact, but then i would have had to add a front-mater, which I don't want to include in my markdown files. The nice thing about tags is that you can get a quick overview of the content. An alternative would be a search section. This would serve the same purposes if suggestions of topics was provided. For instance from previous searches, or popular terms, some sort of context is need. Im still on the fence about this. For now I have search locally through [Atom.io], it would be nice for other people reading the blog to view other similar posts for niche subjects like RegEx etc.

### Redcarpet
[Redcarpet] is a markdown syntax. The great thing about Redcarpet is that it gets the newline problem right. The initial markdown syntax made by [daringfireball]  was flawed in that it didn't include any intuitive way to add line breaks. Double space after each line then became the way to get line-breaks. Why wasn't this included? Hint: 80char soft-wrap. There is loads of gossip around this problem on stackoverflow. Not only that Redcarpet was the only markdown parser I got working with regular code fencing when hosting on [GitHub] Redcarpet also has highlighting, underlining, emphasizing and striking. And since github pages supports it, there may also be a future for the Redcarpet standard. I would prefer to use the [kramdown] parser with GHM "GitHubMarkdown" but that didn't work with code-fencing on github. I would prefer it because then I could use the same newline convention with Github wiki as Github pages. Anyways this isn't a big deal. Just mind those double spaces when blogging.

### Github
Github can work as a last resort CMS for iPhone & iPad. It can edit files and create new once. However I will start to use [Textastic]  +  iCloud + [GitSync] as the Mobile CMS suit. As Textastic support markdown and iCloud. ==On a Mac-Mini-Server the [GitSync] app takes care of syncing the iCloud files up to Github==. This is a solution until [GitSync] becomes a native app for IOS. And works fine as long you keep the file count bellow 50.000, after that iCloud will start to get buggy. 

### Embedding images & video through dropbox
Dropbox has 2GB of free image hosting. Github can host the images as well, but I prefer keeping the blog small, This is better for mobile workflows in the future as well, where the notion a file system is absent. However IOS has great dropbox capabilities. You can upload photos in native IOS apps like iPhoto and safari and most other apps as well that has a share button. 

I keep my public dropbox folder in my OSX dock so that it's always ready for images. Then I use [textexpander] to expand the img-html-markup:

```html
Regular image:
![img](https://www.google.com/logo.png) 
Retina image:
<img width="320" alt="img" src="https://www.google.com/logo.png">
```
_![desk](https://raw.githubusercontent.com/stylekit/img/master/computer screen 232323.png)_ 
Here is an example on adding video to your markdown: 

```
<video src="https://dl.dropboxusercontent.com/u/2559476/stylekit_anim828520342.mov" type="video/h.264" autoplay="autoplay" muted="muted" preload="preload" loop="loop" width="480"></video>
```

Output:

<video src="https://dl.dropboxusercontent.com/u/2559476/stylekit_anim828520342.mov" type="video/h.264" autoplay="autoplay" muted="muted" preload="preload" loop="loop" width="480"></video>

### Foot-notes & linked-paragraphs & recycling links
Jekyll offers Wikipedia style foot-notes, great when using citations in research papers or providing sources without breaking up the flow of the article. There is also Github style linkable paragraphs. You can also recycle often used links in an article by adding all the links in the bottom of the article. And then just use this syntax: `[name-of-link]` to grab the links, this is great for editing many links if the links are subjected to change in the future, or if the links are really long and break up the flow of your markdown text. Then links at the bottom of the article is not added to the rendered html. ==These 3 features combined with the highlighting feature makes for a great research tool for students and researchers==. There is also hidden comments, if you want to make comments in your article that you don't want to be visible to the reader. `<!--hidden comment-->`

### Domain-name-provider
I use [namecheap] as the domain provider, they offer free whois guard. And the DNS is pretty straight forward to setup in regards to the github pages. A couple of [stackoverflow]  searches on the subject and the DNS forward to [github pages] etc is done.

### Why Jekyll?
>Society as a whole can benefit from using open-source software, if the countries of the world started to use open-source rather than paying inflated fees to private companies, the progress of open-source technology would skyrocket 🚀`<--Emojies!!!`. And the world could start to reap the fruits of said technology. 

When was the last time you heard a private company hired by the state do anything useful outside what the state paid them to do? A first step into this new paradigm is [healthcare.gov] And their use of Jekyll. Hopefully Jekyll will gain more traction and get better because of this. 

Another upside of using the [Jekyll] + [GitSync] + [GitHub] combo is that everything you write is automatically version controlled. Unlike Evernote which only version control a couple of times per day[^2]. Every time you save your article the article is synced to github, so at any point in the future you want to roll back a few deleted paragraphs you can. In theory writing apps should stop implementing the undo feature because its being taken care of. ==Every state your text has ever been in is revertible==. 

==The most important aspect of Jekyll is that its 100% secure forever==. Because its just static pages with html. This means that you don't have to invest anything in securing your website. They can live on forever and not have to worry about exploits etc that are discovered daily with systems like [wordpress]  and other database driven websites. Another important aspect about Jekyll & Github is that its **100% free**. Keeping things cost efficient is very important. Keeping that philosophy throughout the lifecycle of projects is even more important[^3].  

### In closing:
It took about 3 days to research, learn, contribute and implement a good workflow with Jekyll. It could have taken a lot less, but I wanted to see the bounds of Jekyll so I looked into everything. Here are some of my notes from my [Jekyll-research-notes]. And here is the markdown file of this article: [The-Jekyll-Setup.md]

### Tasks:
- [ ] Figure out how to not use the - char in the filenames etc maybe use: `{{ post.path }}` post.path or post.url and then extract the title from that
- [ ] Create a short video when you have the Mobile CMS suit up and running
- [ ] write a more comprehensive about page while keeping it simple.
- [x] investigate if you can do blog.stylekit.org/blogposthere/, you can but its not needed
- [ ] embed .mp4 or keep using gifs? .mp4 files doesn't always seem to work on the iPad.
- [ ] create a roadmap post?
- [ ] add gitsync.io the same way, or wait?
- [ ] add goto links for the paragraphs? [link](https://george-hawkins.github.io/basic-gfm-jekyll/redcarpet-extensions.html#hard-wrap) 
- [x] fix the blog post titles getting every word capitalized
- [ ] Do some more digging into why the title words get converted before you get them
- [ ] make a "-" inserter between words generator in [textexpander] js
- [x] remove the <!--more--> from the end html
- [x] fav icon and pinned icon: [link](https://developer.apple.com/library/mac/releasenotes/General/WhatsNewInSafari/Articles/Safari_9.html#//apple_ref/doc/uid/TP40014305-CH9-SW20) 
- [ ] ==Get the paragraph links working==, so that you can refer people to specific parts in an article etc. or even refer paragraphs within the same article, see `[paragraph](A)` etc, there is a post about this on stackoverflow somewhere, or was it on Jekyll's website? [Paragraph linking]
- [ ] Try using the cite syntax for citations and the quote syntax for quotations. 
- [ ] Write a pro & con list about Jekyll
- [ ] Write an article about how you use Jekyll blogging as a project management / todo-list / progress-tracker / wikipedia platform

<!--Foot-notes--> 
[^1]: Full disclosure: Im the creator of [GitSync] so my opinions about the usage of this technology is 100% biased. I really believe in the implications of what [GitSync] enables humanity to do. 
[^2]: "Free: The Future of a Radical Price" a book by Chris Anderson editor of Wired Magazine http://www.amazon.com/Free-The-Future-Radical-Price/dp/1401322905
[^3]: link to citation about Evernote version control update frequency: [Evernote article]

<!--Links, this section wont be visible to the readers-->
[wordpress]: http://www.wordpress.com/
[wikipedia]: http://www.wikipedia.com/
[stackoverflow]: http://www.stackoverflow.com/
[disqus]: http://www.disqus.com/
[GitHub]: http://www.github.com/
[healthcare.gov]: https://developmentseed.org/blog/new-healthcare-gov-is-open-and-cms-free/
[Textastic]: http://www.textasticapp.com
[Redcarpet]:(https://github.com/vmg/redcarpet)
[Atom.io]: www.atom.io
[formspring]: www.formspring.com
[Pixyll]: https://github.com/johnotander/pixyll
[daringfireball]: www.daringfireball.com
[Jekyll]: http://www.jekyllrb.com
[textexpander]: https://smilesoftware.com/textexpander
[kramdown]: https://github.com/gettalong/kramdown
[GitSync]: https://github.com/eonist/GitSyncOSX
[namecheap]: www.namecheap.com
[google analytics]: https://www.google.com/analytics/
[github pages]: https://pages.github.com/
[Evernote article]: https://blog.evernote.com/blog/2010/04/14/new-premium-features-note-history-and-50mb-notes/
[The-Jekyll-Setup.md]: https://raw.githubusercontent.com/stylekit/stylekit.github.io/master/_posts/2016-01-06-The-Jekyll-Setup.md
[Jekyll-research-notes]: https://github.com/eonist/research/wiki/Jekyll-research
[Paragraph linking]: https://milanaryal.com/2015/adding-hover-anchor-links-to-header-on-github-pages-using-jekyll/