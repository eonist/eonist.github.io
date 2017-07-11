<!--more--> 

Apparently if you delete .css files then your cached styles might crash. A temp solution is to reset the cache styles.xml file with the bellow code:

```xml
<data>
	<cssFileDates>
	</cssFileDates>
	<styles>
	</styles>
</data>
```

This bug will be resolved later