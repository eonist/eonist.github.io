An algorithm that positions tree structured data items as an inverted tree. Originally written in swift <!--more--> For the [learn-anything.xyz](https://www.learn-anything.xyz)  project as a way to automate mindmap contributions. 

#### Concept:
The algorithm makes sure the that parents and it's descendants align into a hierarchical structure without overlapping. It's a novel idea that has some challenges. The algorithm is based on functional programming concepts via recursion, reduce, map, filter. Sometimes in combinations. And is not exclusive to swift, the same algorithm should be easy enough to port to JavaScript. The algorithm is Open-Source MIT and can be found here: [the github project](https://github.com/carg0/hierarchical-spatial-algo) 

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

#### The math behind the algorithm
I spent a full day just thinking about how to figure out how to align the hierarchy. Drawing on paper helped a lot as it allows your brain to spend less energy on visualising the design in your head. Your also able to see connections you wouldn't be able to see if you just tried to code the algorithm straight away. Having spent a day just thinking about the problem and jotting down ideas on how to lay out the code. The actual code took about 3-4 hours to write. I procrastinated writing it until at around 20:00 when I just said ok lets do this! I finished a little after midnight and presented it to team learn-anything. Then the next day I spent an hour or two cleaning/refactoring the code up a bit. I usually don't attempt to write algorithms at 20:00 at night but since I sort of already had solved it on paper it was just a mater of converting the idea to code and was more implementation work rather than high concept coding. 

#### Future ideas:
By customizing the vector line styles. This spatial algorithm can be used to achieve designs like this:

<img width="640" alt="img" src="https://raw.githubusercontent.com/stylekit/img/master/mindnode_muskateller.gif">

From mindnode.com via this tweet: https://twitter.com/fafner/status/859736637995192323 


