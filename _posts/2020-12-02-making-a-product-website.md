My notes on building a product website<!--more-->

For me it's hard to use something existing and trying to customize it. When something has matured, it's hard to wrangle it to be something else. Unless the changes are miniscule. They often are not. Building something from scratch is sort of simpler, because you can always start with simple bricks and a state that is pristine and has no legacy and things to worry about. But what do you start with?

### The process
- 🎨 Start by drawing in a notebook, to get a feel for what to build
- 🖥 Then start designing each page in sketch for macOS (Desktop and optionally mobile)
- 🙉 Then start building each page in isolation in HTML+CSS, just get the layout roughly complete
- Then start to connect the pages together in HTML + css
- Then start refining the css by adding images, fonts, colors, dropshadows etc
- Then start making subpages, and link them up, so everything is clickable and works (or jut make one landing page)
- Then add **responsive.css** to support mobile screens, make sure the website also works in horizontal layout on mobile devices
- (Optional) Then re-build the html in Jekyll, so that we can have a blog, and reuse html/css elements across pages
- (Optional) Add Google analytics, MailChip to the newsletter component, and add Custom google fonts


### Blueprint / mockup 

Sketch out the design in a notebook, one for desktop, and one for mobile. And use figma or **sketch** to make the **blueprints** how the site should look. Use a 12 column grid with gutter and margins. Google this to get the sizes needed. Elements should snap to the 12 column grid. try to use simple row spacing. like 6, 12, 24, 32, 48, 96, 128 etc.

### Gotchas
- We do things in steps to not lose our bearings. Modern websites are complex, focusing on a subset at each step, makes things easier
- Jekyll enables you to host your site for free on github, deployment is easily done via Github desktop.
- Always start with pure CSS and HTML before adding backend and javascripts etc
- Just begin. Start a markdown page and write only divs with dummy colors. Then improve as you go. Dont try to solve everything from the start etc.
- Get as much feedback as you can after a couple of revisions in sketch.
- It can sometimes be a good idea to make a couple of variations in sketch.

### The concept
- The idea is to make each page make up the size of your macbook / iPhone screen like in the example bellow
- The key to building a responsive webpage is to strip to down to the bare essentials, make sure that works in both mobile and desktop, then add content.
- Try to only work with **divs** with **background colors** in the beginning. Nail this. Then fiddle with image, and text later, which can get finicky

## Iterate forward
- Start by defining your **content in text**. (Use atom and markdown to structure things)
- Then start with `index.html` and `style.css` and `reset.css` (Code in Atom.io and preview in safari)
- Begin to align dummy **color boxes** to how your end result will be (logos, menues, content etc)
- Add headers and footers and content elements. Import other css files for each module etc. Composability is key
- Slowly add text and menues and images
- Then start to add design elements and refine sizes etc.
- Build out the subpages
- Once you have the basic html and css up and running you can start to reuse elements in a CMS like Jekyll

### Enabling https in github-pages and namecheap:
1. Add A records at namecheap that points to github pages ip adresses 185.199.109.153, 185.199.110.153, 185.199.111.153
2. Add cname record that points to the github-pages url xxx.github.io
3. Add cname file to githubpages root domain.xx
4. wait in github settings for the repo. Provisioning of ssl certificates takes 15 min
5. click enforce https


### To recap:

To begin with it can be ideal to create a website/website.md doc and pseudo code in fenced code block. Just to get the basics out first.

1. Start with the fundamentals. **Simple divs**. **Simple colors**. Figure out the basic layout alignments and **page structure**. Just create **pseudo code** at first to just figure out the scope. Then comment out everything but the basics, and slowly convert the pseudo code to production code. We do this because HTML and CSS is such a messy language, and you often times get stuck if you start coding production code from the beginning. Basics of css: `#id` is more unique elements and `.class` common reusable elements (CSS, HTML)
2. Add Simple text and dummy images (CSS, HTML)
3. Start adding links and sub-sites (CSS, HTML)
4. Start moving html code and css into a backend system, jekyll wordpress etc
5. Once the site works for desktop. It's now time to retro-fit it for mobile.

### Gotchas:
- Use safari as the preview of the index.html
- Use a text editor for the html and css. Something like: VSCode
- Add a **reset.css** file to make it easier to style without interference from default browser styles etc
- In the beginning don't try to be too clever with styling, just use the simplest style to test things out. Do clever things later when things start to take shape
