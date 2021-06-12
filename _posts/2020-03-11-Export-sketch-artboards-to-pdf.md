My notes on how to export sketch artboards to pdf<!--more-->

### Problem:
You can use the built in export artboards to pdf feature. But the problem is that it creates a pdf with all the raw resources in the sketch project. sometimes its over 100mb. The bellow solution creates a small pdf while retaining quality. You can use an online pdf convert to do the same thing. But quality may end up worse than you expect.

### Sketch artboards to pdf

1. Make multiple artboards
2. Name the artboards chronologically 01-front, 02-about, 03-features etc
3. File -> Export as png files (set individual artboard resolution if needed)
4. Open apples own Automator and search for image to pdf
5. Drag the png images above the image to pdf automation
6. Hit play and your .pdf is generated
