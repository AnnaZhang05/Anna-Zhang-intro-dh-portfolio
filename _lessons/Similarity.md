---
layout: page
title: Using Common Similarity Measures for Text Analysis
description: This is the code for the PH lesson on common similarity measures. 
---

## Source
[John R. Ladd, "Understanding and Using Common Similarity Measures for Text Analysis," Programming Historian (2020), https://doi.org/10.46430/phen0089.](https://programminghistorian.org/en/lessons/common-similarity-measures#calculating-distance-in-python)
## Reflection

Before starting the third and final programming historian lesson for the course, I did not expect to get stuck on it for such an extended period of time due to a very simple mistake. For my last lesson, I chose the common similarity measures for text analysis mainly because of its "text analysis" aspect, since I didn't know anything about common similarity measures but I'm doing stanza treebank comparisons for my final project, I thought text analysis would help.

I got through the majority of the lesson with ease, and trying to understand most of the mathematical formulas/explanations from my high school geoemtry class. In the introduction/preparation sections of the lesson, the example grpahs of text similarity helped me make sense of the concepts a lot more than just the formulas. 

Using mathematical concepts like Euclidian geometry to calculate and interpret similarities between different texts is definitely something within the DH spheres that I never considered or thought of. By the most textbook definition, this lesson combines a lot of the basic digital/mathematical concepts with the humanities perfectly. 

However, I ran into a problem with the code of the lesson about halfway through, consistently getting an error code after running the "top5_euclidean" scripts. After spending about 2 weeks troubleshooting with Peter, we finally discovered the very basic error that produced so much chaos and anxiety: the operating system differences. In the "filekeys = [f.split('\\')[-1].split('.')[0] for f in filenames]" line, the original lesson provided "f.split('/')" for the split command, which is assumed to be a non-Windows operating system command. Since I use a Windows laptop, I kept getting the same error when pulling up metadata for the euclidian distances due to the slash differences. 

After that, I was able to finally finish the lesson and reflect upon my learning experience that uses Euclidian geometry to compare different length texts using Python. Overall, if the lesson author was able to take into operating system difference, my experience would be greatly improved, since my efficiency would increase, thus allowing me to retain the information better instead of being frustrated over the same error message. 

## Code 
```python
import glob
import pandas as pd
from sklearn.feature_extraction.text import CountVectorizer
from scipy.spatial.distance import pdist, squareform
```


```python
filenames = glob.glob("1666_texts/*.txt")
filekeys = [f.split('\\')[-1].split('.')[0] for f in filenames]
vectorizer = CountVectorizer(input="filename", max_features=1000, max_df=0.7)
wordcounts = vectorizer.fit_transform(filenames).toarray()

```


```python
metadata = pd.read_csv("1666_metadata.csv", index_col="TCP ID")

```


```python
euclidean_distances = pd.DataFrame(squareform(pdist(wordcounts)), index=filekeys, columns=filekeys)
top5_euclidean = euclidean_distances.nsmallest(6, 'A28989')['A28989'][1:]
print(top5_euclidean)
print(metadata.loc[top5_euclidean.index, ['Author','Title','Keywords']])
```

    A62436     988.557029
    A43020     988.622274
    A29017    1000.024000
    A56390    1005.630151
    A44061    1012.873141
    Name: A28989, dtype: float64
                                   Author  \
    A62436    Thomson, George, 17th cent.   
    A43020    Harvey, Gideon, 1640?-1700?   
    A29017      Boyle, Robert, 1627-1691.   
    A56390     Parker, Samuel, 1640-1688.   
    A44061  Hodges, Nathaniel, 1629-1688.   
    
                                                        Title  \
    A62436  Loimotomia, or, The pest anatomized in these f...   
    A43020  Morbus anglicus: or, The anatomy of consumptio...   
    A29017  The origine of formes and qualities, (accordin...   
    A56390  A free and impartial censure of the Platonick ...   
    A44061  Vindiciæ medicinæ & medicorum: or An apology f...   
    
                                                     Keywords  
    A62436  Hodges, Nathaniel, 1629-1688. -- Vindiciae med...  
    A43020               Tuberculosis -- Early works to 1800.  
    A29017  Matter -- Constitution -- Early works to 1800....  
    A56390     Platonists. Empiricism -- Early works to 1800.  
    A44061  Medicine -- Early works to 1800. Plague -- Eng...  
    


```python
cosine_distances = pd.DataFrame(squareform(pdist(wordcounts, metric='cosine')), index=filekeys, columns=filekeys)

top5_cosine = cosine_distances.nsmallest(6, 'A28989')['A28989'][1:]
print(top5_cosine)

print(metadata.loc[top5_cosine.index, ['Author','Title','Keywords']])
```

    A29017    0.432181
    A43020    0.616269
    A62436    0.629395
    A57484    0.633845
    A60482    0.663113
    Name: A28989, dtype: float64
                                    Author  \
    A29017       Boyle, Robert, 1627-1691.   
    A43020     Harvey, Gideon, 1640?-1700?   
    A62436     Thomson, George, 17th cent.   
    A57484  Rochefort, César de, b. 1605.   
    A60482         Smith, John, 1630-1679.   
    
                                                        Title  \
    A29017  The origine of formes and qualities, (accordin...   
    A43020  Morbus anglicus: or, The anatomy of consumptio...   
    A62436  Loimotomia, or, The pest anatomized in these f...   
    A57484  The history of the Caribby-islands, viz, Barba...   
    A60482  Gērochomia vasilikē King Solomons portraitur...   
    
                                                     Keywords  
    A29017  Matter -- Constitution -- Early works to 1800....  
    A43020               Tuberculosis -- Early works to 1800.  
    A62436  Hodges, Nathaniel, 1629-1688. -- Vindiciae med...  
    A57484     Natural history -- West Indies. Carib Indians.  
    A60482  Bible. -- O.T. -- Ecclesiastes XII, 1-6 -- Par...  
    


```python

```
