An algorithm that positions tree structured data items as an inverted tree written in swift <!--more--> For the [learn-anything.xyz](www.learn-anything.xyz)  project.

**From this:**

```json
{
	"title":"pet grooming",
	"contributors":["Nikita","Angelo","PGG✌️"],
	"meta":["Pets","animals","nature"],
	"nodes":[
		{
			"title":"Tools",
			"nodes":[
				{"title":"clippers","url":"animals/clippers.json"},
				{"title":"Blades"},
				{"title":"Brushes"}
			]
		},
		{
			"title":"Workflows",
			"nodes":[
				{"title":"bathing"},
				{"title":"fur"},
				{"title":"claw"}
			]
		},
		{
			"title":"Tips",
			"nodes":[
				{"title":"Blogs"},
				{"title":"Books"}
			]
		}
	]
}
```
**To this:**

<img width="1440" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2017-05-22 at 02.39.34.png">

### Ascending symmetrically:

<img width="1440" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/petgrooming.png">

This spatial algorithm can be used to achieve similar results as this:

<img width="640" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/mindnode_muskateller.gif">

From this tweet: https://twitter.com/fafner/status/859736637995192323 


[the github project](https://github.com/carg0/hierarchical-spatial-algo) 