An algorithm that positions tree structured data items as an inverted tree written in swift <!--more--> For the [learn-anything.xyz](www.learn-anything.xyz)  project as a way to automate mindmap contributions. 

#### Concept:
The algorithm makes sure the that parents and it's descendants align into a hierarchical structure. It's a novel idea that has some challenges. The algorithm is based on functional programming theories like recursiveness, reduce, map, filter. Sometimes in combinations. And is not exclusive to swift, the same result can be achieved in JavaScript. The algorithm is Open-Source MIT and can be found here: [the github project](https://github.com/carg0/hierarchical-spatial-algo) 

**From this json structure:**

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
**To this graphical representation:**

<img width="1440" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/petgrooming.png">

#### Ascending symmetrically
 Basically all parents are aligned centrally above its descendants

<img width="1440" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/Screen Shot 2017-05-22 at 02.39.34.png">


#### Future ideas:
By customizing the vector lines. This spatial algorithm can be used to achieve similar results as this:

<img width="640" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/mindnode_muskateller.gif">

From mindnode.com via this tweet: https://twitter.com/fafner/status/859736637995192323 


