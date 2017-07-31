My notes on bundling files in an xcode project<!--more--> 


1. Create a test.txt file and put it in a folder named test.bundle
2. drag and drop it next to your .app file in xcode (copy)
3. `Swift.print(Bundle.main.resourcePath!+"/temp.bundle/test.txt")`