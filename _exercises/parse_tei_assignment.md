---
layout: page
title: TEI Parsing Assignment 
description: Notebook for TEI footnote parsing for Gibbon's Decline and Fall of Roman Empire. From week 14, Fall '22.
---
# Parsing a TEI Document - Assignment

## Instructions

1. Parse the tei text of Gibbon's _Decline and Fall_ as found in `gibbon.xml`.
2. Extract all the footnotes and remove extraneous white space.
3. Place footnotes in a dataframe.
4. Save the dataframe as a csv file.

## Parsing TEI


```python
# imports
from bs4 import BeautifulSoup
import re
import pandas as pd
```


```python
cd in_class
```

    C:\Users\17742\Desktop\CLS_161\tei\in_class
    


```python
with open('gibbon.xml', encoding = 'utf8', mode = 'r') as f :
    tei_string = f.read()
```


```python
# use BeatifulSoup to instntiate tei object
tei_object = BeautifulSoup(tei_string)
```


```python
# find all margin notes
footnotes = tei_object.find_all("note", attrs = {'place': 'bottom'})
len(footnotes)
```




    828




```python
# clean footnotes and add to a list
clean_footnotes = []
for f in footnotes: 
    footnote_text= re.sub(r'[\ \n]{2,}', '', f.text)
    footnote_text = footnote_text.replace("\n", "")
    clean_footnotes.append(footnote_text)
clean_footnotes[:5]
```




    ['Pons Aureoli,thirteen miles from Bergamo, and thirty-two from Milan. See Cluver. Italia Antiq. tom. i. p. 245. Near this place, in the year 1703, the obſtinate battle of Caſſano was fought between the French and Auſtrians. The excellent relation of the Chevalier de Folard, who was preſent, gives a very diſtinct idea of the ground. See Polybe de Folard, tom. iii. p. 223—248.',
     'On the death of Gallienus, ſee Trebellius Pollio in Hiſt. Auguſt. p. 181. Zoſimus, l. i. p. 37. Zonaras, l. xii. p. 634. Eutrop. ix. 11. Aurelius Victor in Epitom. Victor in Caeſar. I have compared and blended them all, but have chiefly followed Aurelius Victor, who ſeems to have had the beſt memoirs.',
     'Some ſuppoſed him, oddly enough, to be a baſtard of the younger Gordian. Others took advantage of the province of Dardania, to deduce his origin from Dardanus, and the ancient kings of Troy.',
     'Notoria,a periodical and official diſpatch which the Emperors received from thefrumentarii,or agents diſperſed through the provinces. Of theſe we may ſpeak hereafter.',
     'Hiſt. Auguſt. p. 208. Gallienus deſcribes the plate, veſtments, &c. like a man who loved and underſtood thoſe ſplendid trifles.']




```python
# convert list to dataframe
df = pd.DataFrame(clean_footnotes)
```


```python
# check dataframe
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Pons Aureoli,thirteen miles from Bergamo, and ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>On the death of Gallienus, ſee Trebellius Poll...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Some ſuppoſed him, oddly enough, to be a baſta...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Notoria,a periodical and official diſpatch whi...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hiſt. Auguſt. p. 208. Gallienus deſcribes the ...</td>
    </tr>
  </tbody>
</table>
</div>




```python
# save datafram as csv
df.to_csv('footnotes.csv')
```


```python

```
