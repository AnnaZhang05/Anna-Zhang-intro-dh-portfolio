---
layout: page
title: Counting Word Frequencies with Python
description: This lesson reviews Python dictionary and list applications to sort word frequencies. 
---
## Source
[William J. Turkel and Adam Crymble, "Counting Word Frequencies with Python," Programming Historian 1 (2012), https://doi.org/10.46430/phen0003.](https://programminghistorian.org/en/lessons/counting-frequencies#files-needed-for-this-lesson)

## Reflection
Although we've already covered Python dictionaries and lists in class, as someone that is new to this programming language I thought it would be useful to review their applications through this PH lesson. 

This goes back to the earlier weeks of Python crash course/basics, which I still appreciate even as we've progressed into the more complex zones with NLP and NER. Through the review lesson and splitting strings into lists, I was able to review the syntax of Python. It also reminded me of the removing stop words excercise we did earlier, but I think our approach in class was more efficient for the lines of code it took vs. this lesson. 

This lesson was a much more successful experience compared to the previous ones (counting the failures), not only because it is a review, but also because of it's streamlined tutorial. The jump-start on the lesson in a sequence did not pose as a challenge either, for the authors of this lesson provided a zip file that contained past code/data so the lessonw as ready to use/learn. I appreciated the easy start and minimal setup requirement, as it really alleviated my anxieties towards PH lessons due to my prior attempts.

## Code

```python
import urllib.request, urllib.error, urllib.parse
import obo

url = 'http://www.oldbaileyonline.org/browse.jsp?id=t17800628-33&div=t17800628-33'

response = urllib.request.urlopen(url)
html = response.read().decode('UTF-8')
text = obo.stripTags(html).lower()
fullwordlist = obo.stripNonAlphaNum(text)
wordlist = obo.removeStopwords(fullwordlist, obo.stopwords)
dictionary = obo.wordListToFreqDict(wordlist)
sorteddict_15 = list(obo.sortFreqDict(dictionary))[:15]

for s in sorteddict_15: 
    print(str(s))
```

    (25, 'house')
    (20, 'yes')
    (20, 'prisoner')
    (19, 'mr')
    (17, 'man')
    (15, 'akerman')
    (14, 'mob')
    (13, 'black')
    (12, 'night')
    (11, 'saw')
    (9, 'went')
    (9, 'sworn')
    (9, 'room')
    (9, 'pair')
    (9, 'know')
    
