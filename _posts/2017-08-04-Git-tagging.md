My notes on tagging <!--more--> 	

You need to tag if you want to use SPM. Before you tag make sure you update your nested SPM package manifesto documents with the new tags so they can start pulling in the new code. Basically start updating the tags on the farthest packages in the SPM hierarchy. Then update the root package the last. So update Package.swift file, then tag in git. 

1. Open terminal
2. `cd ~/dev/MyAppProject/`
3. `git tag` will show current tags
4. `git 0.0.0-alpha.4` will add a tag to the local repo
5. `git push origin --tags` will upload the new tag to github

### Adding "build metadata identifier" to a tag:

`git tag v1.0.0 f4ba1fc`

with f4ba1fc being the beginning of the hash of the commit you want to tag and v1.0.0 being the version you want to tag.

Also you add a message at the end, using -m, something like this:

git tag -a 1.0.2 e50f795 -m "my message"

git tag -a 0.0.1 74e0e6f -m "my message"