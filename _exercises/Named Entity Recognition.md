---
layout: page
title: Name Entity Recognition Assignment
description: Notebook with code for NER assignment homework. From week 10, Fall '22.
---

# Named Entity Recognition (NER)

Named entity recognition (NER) is a branch of natural language processing that focuses on extracting the text of names or other semantically distinct ideas from a larger text and tagging according to its special meaning within a system. NER can be broken into two broad categories: algorithms which use deterministic rules to find names (find all tokens that match the regular expression: `([A-Z][a-z]+)`) and statisical models which make guesses about where an entity begins and ends.

In this class, we'll use `spaCy`'s small English NER (`en_core_web_sm`) model to explore how statistical models can recognize named entities. Then, we'll see some of the practical considerations we must take when working with NER. Our workflow will look like this:
* Reacquaint ourselves with `spaCy`
* Pass a single chapter of Gibbon's *Decline and Fall* into the `spaCy` parser
* Examine the NER results
* Filter out all place names
* Use the Pleiades gazetteer to get coordinates for all valid place names
* Save data to CSV

Finally, you'll have the chance to boil down our process into a function and test it out on other chapters from Gibbon (or other texts). Keep in mind that, for next class, you must have your own version of the data we produce today. That's because, next week, Carolyn Talmadge from the DataLab will be showing us how to turn our CSVs into webmaps and applications. 

## Preparing our data


```python
# redownload spaCy's small model - should see 'requirement already satisfied'
!python -m spacy download en_core_web_sm
```

    Collecting en-core-web-sm==3.4.1
      Downloading https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-3.4.1/en_core_web_sm-3.4.1-py3-none-any.whl (12.8 MB)
    Requirement already satisfied: spacy<3.5.0,>=3.4.0 in c:\users\17742\anaconda4\lib\site-packages (from en-core-web-sm==3.4.1) (3.4.2)
    Requirement already satisfied: spacy-legacy<3.1.0,>=3.0.10 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.0.10)
    Requirement already satisfied: typer<0.5.0,>=0.3.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.4.2)
    Requirement already satisfied: cymem<2.1.0,>=2.0.2 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.7)
    Requirement already satisfied: pathy>=0.3.5 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.6.2)
    Requirement already satisfied: requests<3.0.0,>=2.13.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.27.1)
    Requirement already satisfied: pydantic!=1.8,!=1.8.1,<1.11.0,>=1.7.4 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.10.2)
    Requirement already satisfied: murmurhash<1.1.0,>=0.28.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.0.9)
    Requirement already satisfied: spacy-loggers<2.0.0,>=1.0.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.0.3)
    Requirement already satisfied: numpy>=1.15.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.21.5)
    Requirement already satisfied: packaging>=20.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (21.3)
    Requirement already satisfied: setuptools in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (61.2.0)
    Requirement already satisfied: wasabi<1.1.0,>=0.9.1 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.10.1)
    Requirement already satisfied: thinc<8.2.0,>=8.1.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (8.1.5)
    Requirement already satisfied: preshed<3.1.0,>=3.0.2 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.0.8)
    Requirement already satisfied: tqdm<5.0.0,>=4.38.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (4.64.0)
    Requirement already satisfied: langcodes<4.0.0,>=3.2.0 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.3.0)
    Requirement already satisfied: catalogue<2.1.0,>=2.0.6 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.8)
    Requirement already satisfied: jinja2 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.11.3)
    Requirement already satisfied: srsly<3.0.0,>=2.4.3 in c:\users\17742\anaconda4\lib\site-packages (from spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.4.5)
    Requirement already satisfied: pyparsing!=3.0.5,>=2.0.2 in c:\users\17742\anaconda4\lib\site-packages (from packaging>=20.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.0.4)
    Requirement already satisfied: smart-open<6.0.0,>=5.2.1 in c:\users\17742\anaconda4\lib\site-packages (from pathy>=0.3.5->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (5.2.1)
    Requirement already satisfied: typing-extensions>=4.1.0 in c:\users\17742\anaconda4\lib\site-packages (from pydantic!=1.8,!=1.8.1,<1.11.0,>=1.7.4->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (4.1.1)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in c:\users\17742\anaconda4\lib\site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (1.26.9)
    Requirement already satisfied: certifi>=2017.4.17 in c:\users\17742\anaconda4\lib\site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2021.10.8)
    Requirement already satisfied: idna<4,>=2.5 in c:\users\17742\anaconda4\lib\site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (3.3)
    Requirement already satisfied: charset-normalizer~=2.0.0 in c:\users\17742\anaconda4\lib\site-packages (from requests<3.0.0,>=2.13.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.4)
    Requirement already satisfied: confection<1.0.0,>=0.0.1 in c:\users\17742\anaconda4\lib\site-packages (from thinc<8.2.0,>=8.1.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.0.3)
    Requirement already satisfied: blis<0.8.0,>=0.7.8 in c:\users\17742\anaconda4\lib\site-packages (from thinc<8.2.0,>=8.1.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.7.9)
    Requirement already satisfied: colorama in c:\users\17742\anaconda4\lib\site-packages (from tqdm<5.0.0,>=4.38.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (0.4.4)
    Requirement already satisfied: click<9.0.0,>=7.1.1 in c:\users\17742\anaconda4\lib\site-packages (from typer<0.5.0,>=0.3.0->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (8.0.4)
    Requirement already satisfied: MarkupSafe>=0.23 in c:\users\17742\anaconda4\lib\site-packages (from jinja2->spacy<3.5.0,>=3.4.0->en-core-web-sm==3.4.1) (2.0.1)
    [+] Download and installation successful
    You can now load the package via spacy.load('en_core_web_sm')
    


```python
import pandas as pd
import spacy
nlp = spacy.load('en_core_web_sm') # good idea to initialize here
```


```python
# downloading gibbon text from my gh
import wget
import os
if not os.path.isfile('gibbon_text.csv'):
    wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/gibbon_text.csv')
```

    100% [..........................................................................] 6438024 / 6438024


```python
gibbon_by_chapter = pd.read_csv('gibbon_text.csv').rename(columns={'Unnamed: 0':'chapter'})
gibbon_by_chapter
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
      <th>chapter</th>
      <th>StringText</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Chapter 1</td>
      <td>\nIn the second century of the Christian era, ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Chapter 2</td>
      <td>\nIt is not alone by the rapidity or extent of...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Chapter 3</td>
      <td>\nThe obvious definition of a monarchy seems t...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Chapter 4</td>
      <td>\nThe mildness of Marcus, which the rigid disc...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Chapter 5</td>
      <td>\nThe power of the sword is more sensibly felt...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Chapter 67</td>
      <td>\nThe respective merits of Rome and Constantin...</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Chapter 68</td>
      <td>\nThe siege of Constantinople by the Turks att...</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Chapter 69</td>
      <td>\nIn the first ages of the decline and fall of...</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Chapter 70</td>
      <td>\nIn the apprehension of modern times, Petrarc...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Chapter 71</td>
      <td>\nIn the last days of Pope Eugenius the Fourth...</td>
    </tr>
  </tbody>
</table>
<p>71 rows × 2 columns</p>
</div>




```python
gibbon_by_chapter['StringText'][0]
```




    "\nIn the second century of the Christian era, the empire of Rome comprehended the fairest part of the earth, and the most civilised portion of mankind. The frontiers of that extensive monarchy were guarded by ancient renown and disciplined valour. The gentle, but powerful, influence of laws and manners had gradually cemented the union of the provinces. Their peaceful inhabitants enjoyed and abused the advantages of wealth and luxury. The image of a free constitution was preserved with decent reverence. The Roman senate appeared to possess the sovereign authority, and devolved on the emperors all the executive powers of government. During a happy period of more than fourscore years, the public administration was conducted by the virtue and abilities of Nerva, Trajan, Hadrian, and the two Antonines. It is the design of this and of the two succeeding chapters, to describe the prosperous condition of their empire; and afterwards, from the death of Marcus Antoninus, to deduce the most important circumstances of its decline and fall: a revolution which will ever be remembered, and is still felt by the nations of the earth.\n\nThe principal conquests of the Romans were achieved under the republic; and the emperors, for the most part, were satisfied with preserving those dominions which had been acquired by the policy of the senate, the active emulation of the consuls, and the martial enthusiasm of the people. The seven first centuries were filled with a rapid succession of triumphs; but it was reserved for Augustus to relinquish the ambitious design of subduing the whole earth, and to introduce a spirit of moderation into the public councils. Inclined to peace by his temper and situation, it was easy for him to discover that Rome, in her present exalted situation, had much less to hope than to fear from the chance of arms; and that, in the prosecution of remote wars, the undertaking became every day more difficult, the event more doubtful, and the possession more precarious and less beneficial. The experience of Augustus added weight to these salutary reflections, and effectually convinced him that, by the prudent vigour of his counsels, it would be easy to secure every concession which the safety or the dignity of Rome might require from the most formidable barbarians. Instead of exposing his person and his legions to the arrows of the Parthians, he obtained, by an honourable treaty, the restitution of the standards and prisoners which had been taken in the defeat of Crassus.\nHis generals, in the early part of his reign, attempted the reduction of Aethiopia and Arabia Felix. They marched near a thousand miles to the south of the tropic; but the heat of the climate soon repelled the invaders and protected the unwarlike natives of those sequestered regions. The northern  countries of Europe scarcely deserved the expense and labour of conquest. The forests and morasses of Germany were filled with a hardy race of barbarians, who despised life when it was separated from freedom; and though, on the first attack, they seemed to yield to the weight of the Roman power, they soon, by a signal act of despair, regained their independence, and reminded Augustus of the vicissitude of fortune. On the death of that emperor his testament was publicly read in the senate. He bequeathed, as a valuable legacy to his successors, the advice of confining the empire within those limits which nature seemed to have placed as its permanent bulwarks and boundaries; on the west the Atlantic Ocean; the Rhine and Danube on the north; the Euphrates on the east; and towards the south the sandy deserts of Arabia and Africa.\nHappily for the repose of mankind, the moderate system recommended by the wisdom of Augustus was adopted by the fears and vices of his immediate successors. Engaged in the pursuit of pleasure or in the exercise of tyranny, the first Caesars seldom showed themselves to the armies, or to the provinces; nor were they disposed to suffer that those triumphs which their indolence neglected should be usurped by the conduct and valour of their lieutenants. The military fame of a subject was considered as an insolent invasion of the Imperial prerogative; and it became the duty, as well as interest, of every Roman general, to guard the frontiers intrusted to his care, without aspiring to conquests which  might have proved no less fatal to himself than to the vanquished barbarians.\nThe only accession which the Roman empire received during the first century of the Christian era was the province of Britain. In this single instance the successors of Caesar and Augustus were persuaded to follow the example of the former, rather than the precept of the latter. The proximity of its situation to the coast of Gaul seemed to invite their arms; the pleasing, though doubtful, intelligence of a pearl fishery attracted their avarice; and as Britain was viewed in the light of a distinct and insulated world, the conquest scarcely formed any exception to the general system of continental measures. After a war of about forty years, undertaken by the most stupid, maintained by the most dissolute, and terminated by the most timid of all the emperors, the far greater part of the island submitted to the Roman yoke. The various tribes of Britons possessed valour without conduct, and the love of freedom without the spirit of union. They took up arms with savage fierceness, they laid them down, or turned them against each other with wild inconstancy; and while they fought singly, they were successively subdued. Neither the fortitude of Caractacus, nor the despair of Boadicea, nor the fanaticism of the Druids, could avert  the slavery of their country, or resist the steady progress of the Imperial generals, who maintained the national glory, when the throne was disgraced by the weakest or the most vicious of mankind. At the very time when Domitian, confined to his palace, felt the terrors which he inspired, his legions, under the command of the virtuous Agricola, defeated the collected force of the Caledonians at the foot of the Grampian hills; and his fleets, venturing to explore an unknown and dangerous navigation, displayed the Roman arms round every part of the island. The conquest of Britain was considered as already achieved; and it was the design of Agricola to complete and ensure his success by the easy reduction of Ireland, for which, in his opinion, one legion and a few auxiliaries were sufficient. The western isle might be improved into a valuable possession, and the Britons would wear their chains with the less reluctance, if the prospect and example of freedom was on every side removed from before their eyes.\nBut the superior merit of Agricola soon occasioned his removal from the government of Britain; and for ever disappointed this rational, though extensive, scheme of conquest. Before his departure the prudent general had provided for security as well as for dominion. He had observed that the island is almost divided into two unequal parts by the opposite gulfs or, as they are now called, the Friths of Scotland. Across the narrow interval of about forty miles he had drawn a line of military stations, which was afterwards fortified, in the reign of Antoninus Pius, by a turf rampart, erected on foundations of stone. This wall of Antoninus, at a small  distance beyond the modern cities of Edinburgh and Glasgow, was fixed as the limit of the Roman province. The native Caledonians preserved, in the northern extremity of the island, their wild independence, for which they were not less indebted to their poverty than to their valour. Their incursions were frequently repelled and chastised; but their country was never subdued. The masters of the fairest and most wealthy climates of the globe turned with contempt from gloomy hills assailed by the winter tempest, from lakes concealed in a blue mist, and from cold and lonely heaths, over which the deer of the forest were chased by a troop of naked barbarians.\nSuch was the state of the Roman frontiers, and such the maxims of Imperial policy, from the death of Augustus to the accession of Trajan. That virtuous and active prince had received the education of a soldier, and possessed the talents of a general. The peaceful system of his predecessors was interrupted by scenes of war and conquest; and the legions, after a long interval, beheld a military emperor at their head. The first exploits of Trajan were against the Dacians, the most warlike of men, who dwelt beyond the Danube, and who, during the reign of Domitian, had insulted, with impunity, the majesty of Rome. To the strength and fierceness of barbarians they added a contempt for life, which was derived from a warm persuasion of the immortality and transmigration of the soul. Decebalus, the Dacian king, approved  himself a rival not unworthy of Trajan; nor did he despair of his own and the public fortune, till, by the confession of his enemies, he had exhausted every resource both of valour and policy. This memorable war, with a very short suspension of hostilities, lasted five years; and as the emperor could exert, without control, the whole force of the state, it was terminated by the absolute submission of the barbarians. The new province of Dacia, which formed a second exception to the precept of Augustus, was about thirteen hundred miles in circumference. Its natural boundaries were the Dniester, the Theiss, or Tibiscus, the Lower Danube, and the Euxine Sea. The vestiges of a military road may still be traced from the banks of the Danube to the neighbourhood of Bender, a place famous in modern history, and the actual frontier of the Turkish and Russian Empires.\nTrajan was ambitious of fame; and as long as mankind shall continue to bestow more liberal applause on their destroyers than on their benefactors, the thirst of military glory will ever be the vice of the most exalted characters. The praises of Alexander, transmitted by a succession of poets and historians, had kindled a dangerous emulation in the mind of Trajan. Like him, the Roman emperor undertook an expedition against the nations of the east, but he lamented with a sigh that his advanced age scarcely left him any hopes of equalling the renown of the son of Philip.  Yet the success of Trajan, however transient, was rapid and specious. The degenerate Parthians, broken by intestine discord, fled before his arms. He descended the river Tigris in triumph, from the mountains of Armenia to the Persian gulf. He enjoyed the honour of being the first, as he was the last, of the Roman generals, who ever navigated that remote sea. His fleets ravished the coasts of Arabia; and Trajan vainly flattered himself that he was approaching towards the confines of India. Every day the astonished senate received the intelligence of new names and new nations that acknowledged his sway. They were informed that the kings of Bosphorus, Colchos, Iberia, Albania, Osrhoene, and even the Parthian monarch himself, had accepted their diadems from the hands of the emperor; that the independent tribes of the Median and Carduchian hills had implored his protection; and that the rich countries of Armenia, Mesopotamia, and Assyria, were reduced into the state of provinces. But the death of Trajan soon clouded the splendid prospect; and it was justly to be dreaded that so many distant nations would throw off the unaccustomed yoke, when they were no longer restrained by the powerful hand which had imposed it.\nIt was an ancient tradition that, when the Capitol was founded by one of the Roman kings, the god Terminus (who presided over boundaries, and was represented according to the fashion of that age by a large stone) alone, among all the inferior deities, refused to yield his place to Jupiter himself. A favourable inference was drawn from his obstinacy, which was interpreted by the augurs as a sure presage that the  boundaries of the Roman power would never recede. During many ages, the prediction, as it is usual, contributed to its own accomplishment. But though Terminus had resisted the majesty of Jupiter, he submitted to the authority of the emperor Hadrian. The resignation of all the eastern conquests of Trajan was the first measure of his reign. He restored to the Parthians the election of an independent sovereign; withdrew the Roman garrisons from the provinces of Armenia, Mesopotamia, and Assyria; and, in compliance with the precepts of Augustus, once more established the Euphrates as the frontier of the empire. Censure, which arraigns the public actions and the private motives of princes, has ascribed to envy a conduct which might be attributed to the prudence and moderation of Hadrian. The various character of that emperor, capable, by turns, of the meanest and the most generous sentiments, may afford some colour to the suspicion. It was, however, scarcely in his power to place the superiority of his predecessor in a more conspicuous light, than by thus confessing himself unequal to the task of defending the conquests of Trajan.\nThe martial and ambitious spirit of Trajan formed a very singular contrast with the moderation of his successor. The restless activity of Hadrian was not less remarkable when compared with the gentle repose of Antoninus Pius. The life of the former was almost a perpetual journey; and as he possessed the various talents of the soldier, the statesman, and the scholar, he gratified his curiosity in the discharge of his duty. Careless of the difference of seasons and of climates,  he marched on foot, and bareheaded, over the snows of Caledonia, and the sultry plains of the Upper Egypt; nor was there a province of the empire which, in the course of his reign, was not honoured with the presence of the monarch. But the tranquil life of Antoninus Pius was spent in the bosom of Italy; and, during the twenty-three years that he directed the public administration, the longest journeys of that amiable prince extended no farther than from his palace in Rome to the retirement of his Lanuvian villa.\nNotwithstanding this difference in their personal conduct, the general system of Augustus was equally adopted and uniformly pursued by Hadrian and by the two Antonines. They persisted in the design of maintaining the dignity of the empire, without attempting to enlarge its limits. By every honourable expedient they invited the friendship of the barbarians; and endeavoured to convince mankind that the Roman power, raised above the temptation of conquest, was actuated only by the love of order and justice. During a long period of forty-three years their virtuous labours were crowned with success; and, if we except a few slight hostilities that served to exercise the legions of the frontier, the reigns of Hadrian and Antoninus Pius offer the fair prospect of universal peace. The Roman name was revered among the most remote nations of the earth. The fiercest barbarians frequently submitted their differences to the arbitration of the emperor; and we are informed by a contemporary historian  that he had seen ambassadors who were refused the honour which they came to solicit, of being admitted into the rank of subjects.\nThe terror of the Roman arms added weight and dignity to the moderation of the emperors. They preserved peace by a constant preparation for war; and while justice regulated their conduct, they announced to the nations on their confines that they were as little disposed to endure as to offer an injury. The military strength, which it had been sufficient for Hadrian and the elder Antoninus to display, was exerted against the Parthians and the Germans by the emperor Marcus. The hostilities of the barbarians provoked the resentment of that philosophic monarch, and, in the prosecution of a just defence, Marcus and his generals obtained many signal victories, both on the Euphrates and on the Danube. The military establishment of the Roman empire, which thus assured either its tranquillity or success, will now become the proper and important object of our attention.\nIn the purer ages of the commonwealth, the use of arms was reserved for those ranks of citizens who had a country to love, a property to defend, and some share in enacting those laws which it was their interest, as well as duty, to maintain. But in proportion as the public freedom was lost in extent of conquest, war was gradually improved into an art, and degraded into a trade. The legions themselves, even at the time when they were recruited in the most distant provinces,  were supposed to consist of Roman citizens. That distinction was generally considered either as a legal qualification or as a proper recompense for the soldier; but a more serious regard was paid to the essential merit of age, strength, and military stature. In all levies, a just preference was given to the climates of the north over those of the south; the race of men born to the exercise of arms was sought for in the country rather than in cities, and it was very reasonably presumed that the hardy occupations of smiths, carpenters, and huntsmen would supply more vigour and resolution than the sedentary trades which are employed in the service of luxury. After every qualification of property had been laid aside, the armies of the Roman emperors were still commanded, for the most part, by officers of a liberal birth and education; but the common soldiers, like the mercenary troops of modern Europe, were drawn from the meanest, and very frequently from the most profligate, of mankind.\nThat public virtue, which among the ancients was denominated patriotism, is derived from a strong sense of our own interest in the preservation and prosperity of the free government of which we are members. Such a sentiment, which had rendered the legions of the republic almost invincible, could make but a very feeble impression on the mercenary servants of a despotic prince; and it became necessary to supply that defect by other motives, of a different, but not less forcible nature, — honour and religion. The peasant, or mechanic, imbibed the useful prejudice that he was advanced to the more dignified profession of arms, in which his rank and reputation would depend on his own valour; and that, although the prowess of a private soldier must often escape the notice of fame, his own behaviour might sometimes  confer glory or disgrace on the company, the legion, or even the army, to whose honours he was associated. On his first entrance into the service, an oath was administered to him with every circumstance of solemnity. He promised never to desert his standard, to submit his own will to the commands of his leaders, and to sacrifice his life for the safety of the emperor and the empire. The attachment of the Roman troops to their standards was inspired by the united influence of religion and of honour. The golden eagle, which glittered in the front of the legion, was the object of their fondest devotion; nor was it esteemed less impious than it was ignominious, to abandon that sacred ensign in the hour of danger. These motives, which derived their strength from the imagination, were enforced by fears and hopes of a more substantial kind. Regular pay, occasional donatives, and a stated recompense, after the appointed term of service, alleviated the hardships of the military life, whilst, on the other hand, it was impossible for cowardice or disobedience to escape the severest punishment. The centurions were authorised to chastise with blows, the generals had a right to punish with death; and it was an inflexible maxim of Roman discipline, that a good soldier should dread his officers far more than the enemy. From such laudable arts did the valour of the Imperial troops receive a degree of firmness and  docility, unattainable by the impetuous and irregular passions of barbarians.\nAnd yet so sensible were the Romans of the imperfection of valour without skill and practice, that, in their language, the name of an army was borrowed from the word which signified exercise. Military exercises were the important and unremitted object of their discipline. The recruits and young soldiers were constantly trained, both in the morning and in the evening, nor was age or knowledge allowed to excuse the veterans from the daily repetition of what they had completely learnt. Large sheds were erected in the winter-quarters of the troops, that their useful labours might not receive any interruption from the most tempestuous weather; and it was carefully observed, that the arms destined to this imitation of war should be of double the weight which was required in real action. It is not the purpose of this work to enter into any minute description of the Roman exercises. We shall only remark that they comprehended whatever could add strength to the body, activity to the limbs, or grace to the motions. The soldiers were diligently instructed to march, to run, to leap, to swim, to carry heavy burdens, to handle every species of arms that was used either for offence or for defence, either in distant engagement or in a closer onset; to form a variety of evolutions; and to move to the sound of flutes in the Pyrrhic or martial dance. In the midst of peace, the Roman troops familiarised themselves with the practice of war; and it is prettily remarked by an ancient historian who had fought against them, that the effusion of blood was the only circumstance  which distinguished a field of battle from a field of exercise. It was the policy of the ablest generals, and even of the emperors themselves, to encourage these military studies by their presence and example; and we are informed that Hadrian, as well as Trajan, frequently condescended to instruct the inexperienced soldiers, to reward the diligent, and sometimes to dispute with them the prize of superior strength or dexterity. Under the reigns of those princes, the science of tactics was cultivated with success; and as long as the empire retained any vigour, their military instructions were respected as the most perfect model of Roman discipline.\nNine centuries of war had gradually introduced into the service many alterations and improvements. The legions, as they are described by Polybius, in the time of the Punic wars, differed very materially from those which achieved the victories of Caesar, or defended the monarchy of Hadrian and the Antonines. The constitution of the Imperial legion may be described in a few words. The heavy-armed infantry, which composed its principal strength, was divided into ten cohorts, and fifty-five companies, under the orders of a correspondent number of tribunes and centurions. The first cohort, which always claimed the post of honour and the custody  of the eagle, was formed of eleven hundred and five soldiers, the most approved for valour and fidelity. The remaining nine cohorts consisted each of five hundred and fifty-five; and the whole body of legionary infantry amounted to six thousand one hundred men. Their arms were uniform, and admirably adapted to the nature of their service: an open helmet, with a lofty crest; a breast-plate, or coat of mail; greaves on their legs, and an ample buckler on their left arm. The buckler was of an oblong and concave figure, four feet in length, and two and a half in breadth, framed of a light wood, covered with a bull's hide, and strongly guarded with plates of brass. Besides a lighter spear, the legionary soldier grasped in his right hand the formidable pilum, a ponderous javelin, whose utmost length was about six feet, and which was terminated by a massy triangular point of steel of eighteen inches. This instrument was indeed much inferior to our modern fire-arms; since it was exhausted by a single discharge, at the distance of only ten or twelve paces. Yet, when it was launched by a firm and skilful hand, there was not any cavalry that durst venture within its reach, nor any shield or corslet that could sustain the impetuosity of its weight. As soon as the Roman had darted his pilum, he drew his sword, and rushed forwards to close with the enemy. It was a short well-tempered Spanish blade, that carried a double edge, and was alike suited to the purpose of striking or of pushing; but the soldier was always instructed to prefer the latter use of his weapon, as his own body remained less exposed, whilst he inflicted a more dangerous wound on his adversary. The legion was usually drawn up eight deep; and the regular distance of three feet was left between the files as well as ranks. A body of troops, habituated to preserve  this open order, in a long front and a rapid charge, found themselves prepared to execute every disposition which the circumstances of war, or the skill of their leader, might suggest. The soldier possessed a free space for his arms and motions, and sufficient intervals were allowed, through which seasonable reinforcements might be introduced to the relief of the exhausted combatants. The tactics of the Greeks and Macedonians were formed on very different principles. The strength of the phalanx depended on sixteen ranks of long pikes, wedged together in the closest array. But it was soon discovered, by reflection as well as by the event, that the strength of the phalanx was unable to contend with the activity of the legion.\nThe cavalry, without which the force of the legion would have remained imperfect, was divided into ten troops or squadrons; the first, as the companion of the first cohort, consisted of an hundred and thirty-two men; whilst each of the other nine amounted only to sixty-six. The entire establishment formed a regiment, if we may use the modern expression, of seven hundred and twenty-six horse, naturally connected with its respective legion, but occasionally separated to act in the line, and to compose a part of the wings of the army. The cavalry of the emperors was no longer composed, like that of the ancient republic, of the noblest youths of Rome and Italy, who, by performing their military service on horseback, prepared themselves for the offices of senator  and consul; and solicited, by deeds of valour, the future suffrages of their countrymen. Since the alteration of manners and government, the most wealthy of the equestrian order were engaged in the administration of justice, and of the revenue; and whenever they embraced the profession of arms, they were immediately intrusted with a troop of horse, or a cohort of foot. Trajan and Hadrian formed their cavalry from the same provinces, and the same class of their subjects, which recruited the ranks of the legion. The horses were bred, for the most part, in Spain or Cappadocia. The Roman troopers despised the complete armour with which the cavalry of the East was encumbered. Their more useful arms consisted in a helmet, an oblong shield, light boots, and a coat of mail. A javelin, and a long broad sword, were their principal weapons of offence. The use of lances and of iron maces they seem to have borrowed from the barbarians.\nThe safety and honour of the empire was principally intrusted to the legions, but the policy of Rome condescended to adopt every useful instrument of war. Considerable levies were regularly made among the provincials, who had not yet deserved the honourable distinction of Romans. Many dependent princes and communities, dispersed round the frontiers, were permitted, for a while, to hold their freedom and security by the tenure of military service. Even select troops of hostile barbarians were frequently compelled or persuaded to consume their dangerous valour in remote  climates, and for the benefit of the state. All these were included under the general name of auxiliaries; and howsoever they might vary according to the difference of times and circumstances, their numbers were seldom much inferior to those of the legions themselves. Among the auxiliaries, the bravest and most faithful bands were placed under the command of prefects and centurions, and severely trained in the arts of Roman discipline; but the far greater part retained those arms, to which the nature of their country, or their early habits of life, more peculiarly adapted them. By this institution, each legion, to whom a certain proportion of auxiliaries was allotted, contained within itself every species of lighter troops, and of missile weapons; and was capable of encountering every nation with the advantages of its respective arms and discipline. Nor was the legion destitute of what, in modern language, would be styled a train of artillery. It consisted in ten military engines of the largest, and fifty-five of a smaller size; but all of which, either in an oblique or horizontal manner, discharged stones and darts with irresistible violence.\nThe camp of a Roman legion presented the appearance of a fortified city. As soon as the space was marked out, the  pioneers carefully levelled the ground, and removed every impediment that might interrupt its perfect regularity. Its form was an exact quadrangle; and we may calculate, that a square of about seven hundred yards was sufficient for the encampment of twenty thousand Romans; though a similar number of our own troops would expose to the enemy a front of more than treble that extent. In the midst of the camp, the praetorium, or general's quarters, rose above the others; the cavalry, the infantry, and the auxiliaries occupied their respective stations; the streets were broad and perfectly straight, and a vacant space of two hundred feet was left on all sides, between the tents and the rampart. The rampart itself was usually twelve feet high, armed with a line of strong and intricate palisades, and defended by a ditch of twelve feet in depth as well as in breadth. This important labour was performed by the hands of the legionaries themselves; to whom the use of the spade and the pick-axe was no less familiar than that of the sword or pilum. Active valour may often be the present of nature; but such patient diligence can be the fruit only of habit and discipline.\nWhenever the trumpet gave the signal of departure, the camp was almost instantly broken up, and the troops fell into their ranks without delay or confusion. Besides their arms, which the legionaries scarcely considered as an encumbrance, they were laden with their kitchen furniture, the instruments of fortification, and the provision of many days. Under this weight, which would oppress the delicacy of a modern soldier, they were trained by a regular step to advance,  in about six hours, near twenty miles. On the appearance of an enemy, they threw aside their baggage, and, by easy and rapid evolutions, converted the column of march into an order of battle. The slingers and archers skirmished in the front; the auxiliaries formed the first line, and were seconded or sustained by the strength of the legions; the cavalry covered the flanks, and the military engines were placed in the rear.\nSuch were the arts of war, by which the Roman emperors defended their extensive conquests, and preserved a military spirit, at a time when every other virtue was oppressed by luxury and despotism. If, in the consideration of their armies, we pass from their discipline to their numbers, we shall not find it easy to define them with any tolerable accuracy. We may compute, however, that the legion, which was itself a body of six thousand eight hundred and thirty-one Romans, might, with its attendant auxiliaries, amount to about twelve thousand five hundred men. The peace establishment of Hadrian and his successors was composed of no less than thirty of these formidable brigades; and most probably formed a standing force of three hundred and seventy-five thousand men. Instead of being confined within the walls of fortified cities, which the Romans considered as the refuge of weakness or pusillanimity, the legions were encamped on the banks of the great rivers, and along the frontiers of the barbarians. As their stations, for the most part, remained fixed and permanent, we may venture to describe the distribution of the troops. Three legions were sufficient for Britain. The principal strength lay upon the Rhine and Danube, and consisted of sixteen legions, in the following proportions: two in the Lower, and three in the Upper Germany; one in Rhaetia, one in Noricum, four in Pannonia, three in Maesia, and two in Dacia. The defence  of the Euphrates was intrusted to eight legions, six of whom were planted in Syria, and the other two in Cappadocia. With regard to Egypt, Africa, and Spain, as they were far removed from any important scene of war, a single legion maintained the domestic tranquillity of each of those great provinces. Even Italy was not left destitute of a military force. Above twenty thousand chosen soldiers, distinguished by the titles of City Cohorts and Praetorian Guards, watched over the safety of the monarch and the capital. As the authors of almost every revolution that distracted the empire, the Praetorians will very soon and very loudly demand our attention; but, in their arms and institutions, we cannot find any circumstance which discriminated them from the legions, unless it were a more splendid appearance, and a less rigid discipline.\nThe navy maintained by the emperors might seem inadequate to their greatness; but it was fully sufficient for every useful purpose of government. The ambition of the Romans was confined to the land; nor was that warlike people ever actuated by the enterprising spirit which had prompted the navigators of Tyre, of Carthage, and even of Marseilles, to enlarge the bounds of the world, and to explore the most remote coasts of the ocean. To the Romans the ocean remained an object of terror rather than of curiosity; the whole extent of the Mediterranean, after the destruction of Carthage and the extirpation of the pirates, was included within their provinces. The policy of the emperors was directed only to preserve the peaceful dominion of that sea, and to protect the commerce of their subjects. With these moderate views,  Augustus stationed two permanent fleets in the most convenient ports of Italy, the one at Ravenna, on the Adriatic, the other at Misenum, in the bay of Naples. Experience seems at length to have convinced the ancients that, as soon as their galleys exceeded two, or at the most three ranks of oars, they were suited rather for vain pomp than for real service. Augustus himself, in the victory of Actium, had seen the superiority of his own light frigates (they were called Liburnians) over the lofty but unwieldy castles of his rival. Of these Liburnians he composed the two fleets of Ravenna and Misenum, destined to command, the one the eastern, the other the western division of the Mediterranean; and to each of the squadrons he attached a body of several thousand marines. Besides these two ports, which may be considered as the principal seats of the Roman navy, a very considerable force was stationed at Frejus, on the coast of Provence, and the Euxine was guarded by forty ships, and three thousand soldiers. To all these we add the fleet which preserved the communication between Gaul and Britain, and a great number of vessels constantly maintained on the Rhine and Danube, to harass the country, or to intercept the passage of the barbarians. If we review this general state of the Imperial forces, of the cavalry as well as infantry, of the legions, the auxiliaries, the guards, and the navy, the most liberal computation will not allow us to fix the entire establishment by sea and by land at more than four hundred and fifty thousand men: a military power which, however formidable it may seem, was equalled by a monarch of the last century, whose kingdom was confined within a single province of the Roman empire.\n\nWe have attempted to explain the spirit which moderated, and the strength which supported, the power of Hadrian and the Antonines. We shall now endeavour, with clearness and precision, to describe the provinces once united under their sway, but, at present, divided into so many independent and hostile states.\nSpain, the western extremity of the empire, of Europe, and of the ancient world, has, in every age, invariably preserved the same natural limits; the Pyrenean mountains, the Mediterranean, and the Atlantic Ocean. That great peninsula, at present so unequally divided between two sovereigns, was distributed by Augustus into three provinces, Lusitania, Baetica, and Tarraconensis. The kingdom of Portugal now fills the place of the warlike country of the Lusitanians; and the loss sustained by the former, on the side of the East, is compensated by an accession of territory towards the North. The confines of Grenada and Andalusia correspond with those of ancient Baetica. The remainder of Spain — Gallicia, and the Asturias, Biscay, and Navarre, Leon, and the two Castilles, Murcia, Valencia, Catalonia, and Arragon, — all contributed to form the third and most considerable of the Roman governments, which, from the name of its capital, was styled the province of Tarragona. Of the native barbarians, the Celtiberians were the most powerful, as the Cantabrians and Asturians proved the most obstinate. Confident in the strength of their mountains, they were the last who submitted to the arms of Rome, and the first who threw off the yoke of the Arabs.\nAncient Gaul, as it contained the whole country between the Pyrenees, the Alps, the Rhine, and the Ocean, was of  greater extent than modern France. To the dominions of that powerful monarchy, with its recent acquisitions of Alsace and Lorraine, we must add the duchy of Savoy, the cantons of Switzerland, the four electorates of the Rhine, and the territories of Liege, Luxemburg, Hainault, Flanders, and Brabant. When Augustus gave laws to the conquests of his father, he introduced a division of Gaul equally adapted to the progress of the legions, to the course of the rivers, and to the principal national distinctions, which had comprehended above an hundred independent states. The sea-coast of the Mediterranean, Languedoc, Provence, and Dauphiné, received their provincial appellation from the colony of Narbonne. The government of Aquitaine was extended from the Pyrenees to the Loire. The country between the Loire and the Seine was styled the Celtic Gaul, and soon borrowed a new denomination from the celebrated colony of Lugdunum, or Lyons. The Belgic lay beyond the Seine, and in more ancient times had been bounded only by the Rhine; but a little before the age of Caesar, the Germans, abusing their superiority of valour, had occupied a considerable portion of the Belgic territory. The Roman conquerors very eagerly embraced so flattering a circumstance, and the Gallic frontier of the Rhine, from Basil to Leyden, received the pompous names of the Upper and the Lower Germany. Such, under the reign of the Antonines, were the six provinces of Gaul; the Narbonnese, Aquitaine, the Celtic, or Lyonnese, the Belgic, and the two Germanies.\nWe have already had occasion to mention the conquest of Britain, and to fix the boundary of the Roman province in this  island. It comprehended all England, Wales, and the Lowlands of Scotland, as far as the Friths of Dumbarton and Edinburgh. Before Britain lost her freedom, the country was irregularly divided between thirty tribes of barbarians, of whom the most considerable were the Belgae in the West, the Brigantes in the North, the Silures in South Wales, and the Iceni in Norfolk and Suffolk. As far as we can either trace or credit the resemblance of manners and language, Spain, Gaul, and Britain were peopled by the same hardy race of savages. Before they yielded to the Roman arms, they often disputed the field, and often renewed the contest. After their submission they constituted the western division of the European provinces, which extended from the columns of Hercules to the wall of Antoninus, and from the mouth of the Tagus to the sources of the Rhine and Danube.\nBefore the Roman conquest, the country which is now called Lombardy was not considered as a part of Italy. It had been occupied by a powerful colony of Gauls, who, settling themselves along the banks of the Po, from Piedmont to Romagna, carried their arms and diffused their name from the Alps to the Apennine. The Ligurians dwelt on the rocky coast, which now forms the republic of Genoa. Venice was yet unborn; but the territories of that state, which lie to the east of the Adige, were inhabited by the Venetians. The middle part of the peninsula, that now composes the duchy of Tuscany and the ecclesiastical state, was the ancient seat of the Etruscans and Umbrians; to the  former of whom Italy was indebted for the first rudiments of a civilised life. The Tiber rolled at the foot of the seven hills of Rome, and the country of the Sabines, the Latins, and the Volsci, from that river to the frontiers of Naples, was the theatre of her infant victories. On that celebrated ground the first consuls deserved triumphs, their successors adorned villas, and their posterity have erected convents. Capua and Campania possessed the immediate territory of Naples; the rest of the kingdom was inhabited by many warlike nations, the Marsi, the Samnites, the Apulians, and the Lucanians; and the sea-coasts had been covered by the flourishing colonies of the Greeks. We may remark, that when Augustus divided Italy into eleven regions, the little province of Istria was annexed to that seat of Roman sovereignty.\nThe European provinces of Rome were protected by the course of the Rhine and the Danube. The latter of those mighty streams, which rises at the distance of only thirty miles from the former, flows above thirteen hundred miles, for the most part to the south-east, collects the tribute of sixty navigable rivers, and is, at length, through six mouths, received into the Euxine, which appears scarcely equal to such an accession of waters. The provinces of the Danube soon acquired the general appellation of Illyricum, or the Illyrian frontier, and were esteemed the most warlike of the empire; but they deserve to be more particularly considered under the names of Rhaetia, Noricum, Pannonia, Dalmatia, Dacia, Maesia, Thrace, Macedonia, and Greece.\nThe province of Rhaetia, which soon extinguished the name of the Vindelicians, extended from the summit of the Alps to  the banks of the Danube; from its source, as far as its conflux with the Inn. The greatest part of the flat country is subject to the elector of Bavaria; the city of Augsburg is protected by the constitution of the German empire; the Grisons are safe in their mountains; and the country of Tyrol is ranked among the numerous provinces of the house of Austria.\nThe wide extent of territory which is included between the Inn, the Danube, and the Save, — Austria, Styria, Carinthia, Carniola, the Lower Hungary, and Sclavonia, — was known to the ancients under the names of Noricum and Pannonia. In their original state of independence their fierce inhabitants were intimately connected. Under the Roman government they were frequently united, and they still remain the patrimony of a single family. They now contain the residence of a German prince, who styles himself Emperor of the Romans, and form the centre, as well as strength, of the Austrian power. It may not be improper to observe, that, if we except Bohemia, Moravia, the northern skirts of Austria, and a part of Hungary, between the Theiss and the Danube, all the other dominions of the house of Austria were comprised within the limits of the Roman empire.\nDalmatia, to which the name of Illyricum more properly belonged, was a long, but narrow tract, between the Save and the Adriatic. The best part of the sea-coast, which still retains its ancient appellation, is a province of the Venetian state, and the seat of the little republic of Ragusa. The inland parts have assumed the Sclavonian names of Croatia and Bosnia; the former obeys an Austrian governor, the latter a Turkish pasha; but the whole country is still infested by tribes of barbarians, whose savage independence irregularly marks the doubtful limit of the Christian and Mahometan power.\n\nAfter the Danube had received the waters of the Theiss and the Save, it acquired, at least among the Greeks, the name of Ister. It formerly divided Maesia and Dacia, the latter of which, as we have already seen, was a conquest of Trajan, and the only province beyond the river. If we inquire into the present state of those countries, we shall find that, on the left hand of the Danube, Temeswar and Transylvania have been annexed, after many revolutions, to the crown of Hungary; whilst the principalities of Moldavia and Walachia acknowledge the supremacy of the Ottoman Porte. On the right hand of the Danube, Maesia, which during the middle ages was broken into the barbarian kingdoms of Servia and Bulgaria, is again united in Turkish slavery.\nThe appellation of Roumelia, which is still bestowed by the Turks on the extensive countries of Thrace, Macedonia, and Greece, preserves the memory of their ancient state under the Roman empire. In the time of the Antonines, the martial regions of Thrace, from the mountains of Haemus and Rhodope to the Bosphorus and the Hellespont, had assumed the form of a province. Notwithstanding the change of masters and of religion, the new city of Rome, founded by Constantine on the banks of the Bosphorus, has ever since remained the capital of a great monarchy. The kingdom of Macedonia, which, under the reign of Alexander, gave laws to Asia, derived more solid advantages from the policy of the two Philips; and, with its dependencies of Epirus and Thessaly, extended from the Aegean to the Ionian sea. When we reflect on the fame of Thebes and Argos, of Sparta and Athens, we can scarcely persuade ourselves that so many immortal  republics of ancient Greece were lost in a single province of the Roman empire, which, from the superior influence of the Achaean league, was usually denominated the province of Achaia.\nSuch was the state of Europe under the Roman emperors. The provinces of Asia, without excepting the transient conquests of Trajan, are all comprehended within the limits of the Turkish power. But instead of following the arbitrary divisions of despotism and ignorance, it will be safer for us, as well as more agreeable, to observe the indelible characters of nature. The name of Asia Minor is attributed, with some propriety, to the peninsula which, confined between the Euxine and the Mediterranean, advances from the Euphrates towards Europe. The most extensive and flourishing district westward of Mount Taurus and the river Halys, was dignified by the Romans with the exclusive title of Asia. The jurisdiction of that province extended over the ancient monarchies of Troy, Lydia, and Phrygia, the maritime countries of the Pamphylians, Lycians, and Carians, and the Grecian colonies of Ionia, which equalled in arts, though not in arms, the glory of their parent. The kingdoms of Bithynia and Pontus possessed the northern side of the peninsula from Constantinople to Trebizond. On the opposite side the province of Cilicia was terminated by the mountains of Syria: the inland country, separated from the Roman Asia by the river Halys, and from Armenia by the Euphrates, had once formed the independent kingdom of Cappadocia. In this place we may observe that the northern shores of the Euxine, beyond Trebizond in Asia and beyond the Danube in Europe, acknowledged the sovereignty of the emperors, and received at their hands either tributary princes or Roman garrisons. Budzak, Crim Tartary, Circassia, and Mingrelia are the modern appellations of those savage countries.\n\nUnder the successors of Alexander, Syria was the seat of the Seleucidae, who reigned over Upper Asia, till the successful revolt of the Parthians confined their dominions between the Euphrates and the Mediterranean. When Syria became subject to the Romans, it formed the eastern frontier of their empire; nor did that province, in its utmost latitude, know any other bounds than the mountains of Cappadocia to the north, and, towards the south, the confines of Egypt and the Red Sea. Phoenicia and Palestine were sometimes annexed to, and sometimes separated from, the jurisdiction of Syria. The former of these was a narrow and rocky coast; the latter was a territory scarcely superior to Wales, either in fertility or extent. Yet Phoenicia and Palestine will for ever live in the memory of mankind; since America, as well as Europe, has received letters from the one, and religion from the other. A sandy desert, alike destitute of wood and water, skirts along the doubtful confine of Syria, from the Euphrates to the Red Sea. The wandering life of the Arabs was inseparably connected with their independence, and wherever, on some spots less barren than the rest, they ventured to form any settled habitations, they soon became subjects to the Roman empire.\nThe geographers of antiquity have frequently hesitated to what portion of the globe they should ascribe Egypt. By  its situation that celebrated kingdom is included within the immense peninsula of Africa; but it is accessible only on the side of Asia, whose revolutions, in almost every period of history, Egypt has humbly obeyed. A Roman prefect was seated on the splendid throne of the Ptolemies; and the iron sceptre of the Mamelukes is now in the hands of a Turkish pasha. The Nile flows down the country, above five hundred miles from the tropic of Cancer to the Mediterranean, and marks, on either side, the extent of fertility by the measure of its inundations. Cyrene, situated towards the west and along the sea-coast, was first a Greek colony, afterwards a province of Egypt, and is now lost in the desert of Barca.\nFrom Cyrene to the ocean, the coast of Africa extends above fifteen hundred miles; yet so closely is it pressed between the Mediterranean and the Sahara, or sandy desert, that its breadth seldom exceeds fourscore or an hundred miles. The eastern division was considered by the Romans as the more peculiar and proper province of Africa. Till the arrival of the Phoenician colonies, that fertile country was inhabited by the Libyans, the most savage of mankind. Under the immediate jurisdiction of Carthage it became the centre of commerce and empire; but the republic of Carthage is now degenerated into the feeble and disorderly states of Tripoli and Tunis. The military government of Algiers oppresses the wide extent of Numidia, as it was once united under Massinissa and Jugurtha: but in the time of Augustus the limits of Numidia were contracted; and at least two-thirds of the country acquiesced in the name of Mauritania, with the epithet of Caesariensis. The genuine Mauritania, or country of the Moors, which, from the ancient city of Tingi, or Tangier, was distinguished by the appellation of Tingitana, is represented by the modern  kingdom of Fez. Sallè, on the Ocean, so infamous at present for its piratical depredations, was noticed by the Romans, as the extreme object of their power, and almost of their geography. A city of their foundation may still be discovered near Mequinez, the residence of the barbarian whom we condescend to style the Emperor of Morocco; but it does not appear that his more southern dominions, Morocco itself, and Segelmessa, were ever comprehended within the Roman province. The western parts of Africa are intersected by the branches of Mount Atlas, a name so idly celebrated by the fancy of poets; but which is now diffused over the immense ocean that rolls between the ancient and the new continent.\nHaving now finished the circuit of the Roman empire, we may observe that Africa is divided from Spain by a narrow strait of about twelve miles, through which the Atlantic flows into the Mediterranean. The columns of Hercules, so famous among the ancients, were two mountains which seemed to have been torn asunder by some convulsion of the elements; and at the foot of the European mountain the fortress of Gibraltar is now seated. The whole extent of the Mediterranean Sea, its coasts and its islands, were comprised within the Roman dominion. Of the larger islands, the two Baleares, which derive their names of Majorca and Minorca from their respective size, are subject at present, the former to Spain, the latter to Great Britain. It is easier to deplore the fate  than to describe the actual condition of Corsica. Two Italian sovereigns assume a regal title from Sardinia and Sicily. Crete, or Candia, with Cyprus, and most of the smaller islands of Greece and Asia, have been subdued by the Turkish arms; whilst the little rock of Malta defies their power, and has emerged, under the government of its military Order, into fame and opulence.\nThis long enumeration of provinces, whose broken fragments have formed so many powerful kingdoms, might almost induce us to forgive the vanity or ignorance of the ancients. Dazzled with the extensive sway, the irresistible strength, and the real or affected moderation of the emperors, they permitted themselves to despise, and sometimes to forget, the outlying countries which had been left in the enjoyment of a barbarous independence; and they gradually assumed the licence of confounding the Roman monarchy with the globe of the earth. But the temper, as well as knowledge, of a modern historian require a more sober and accurate language. He may impress a juster image of the greatness of Rome by observing that the empire was above two thousand miles in breadth, from the wall of Antoninus and the northern limits of Dacia to Mount Atlas and the tropic of Cancer; that it extended in length more than three thousand miles, from the Western Ocean to the Euphrates; that it was situated in the finest part of the Temperate Zone, between the twenty-fourth and fifty-sixth degrees of northern latitude; and that it was supposed to contain above sixteen hundred thousand square miles, for the most part of fertile and well-cultivated land.\n"




```python
first_chapter = gibbon_by_chapter['StringText'][0]
```

## Using `spaCy`'s off-the-shelf NER model

This model was trained on a wide variety of sources, so we can't expect it to be completely accurate. We'll revisit that problem soon. We can train our own NER model and it would do better, but this will take some time to do and requires a lot of set up. If you're interested in training your own model, reach out to me and we can work together on it.


```python
# pass the first chapter into spaCy parser
first_chapter_doc = nlp(first_chapter)
```


```python
type(first_chapter_doc)
```




    spacy.tokens.doc.Doc




```python
for entity in first_chapter_doc.ents: # can access NER with the .ents attribute
    print(entity.text, entity.label_, sep='\t')
```

    the second century	DATE
    Christian	NORP
    Rome	GPE
    Roman	NORP
    more than fourscore years	DATE
    Nerva	GPE
    Trajan	GPE
    Hadrian	NORP
    two	CARDINAL
    Antonines	NORP
    two	CARDINAL
    Marcus Antoninus	GPE
    Romans	NORP
    senate	ORG
    seven first centuries	DATE
    Augustus	ORG
    Rome	GPE
    every day	DATE
    Augustus	ORG
    Rome	GPE
    Parthians	NORP
    Crassus	PRODUCT
    Aethiopia	GPE
    Arabia Felix	PERSON
    a thousand miles	QUANTITY
    Europe	LOC
    Germany	GPE
    first	ORDINAL
    Roman	NORP
    Augustus	PERSON
    senate	ORG
    the Atlantic Ocean	LOC
    Rhine	PERSON
    Danube	PERSON
    Euphrates	LOC
    Arabia	GPE
    Africa	LOC
    Augustus	ORG
    first	ORDINAL
    Caesars	ORG
    Imperial	ORG
    Roman	NORP
    Roman	NORP
    the first century	DATE
    Christian	NORP
    Britain	GPE
    Caesar	ORG
    Augustus	ORG
    Gaul	PERSON
    Britain	GPE
    continental	ORG
    about forty years	DATE
    Roman	NORP
    Britons	PERSON
    Caractacus	ORG
    Boadicea	GPE
    Druids	ORG
    Imperial	ORG
    Domitian	PERSON
    Agricola	ORG
    Caledonians	NORP
    Grampian	NORP
    Roman	NORP
    Britain	GPE
    Agricola	ORG
    Ireland	GPE
    one	CARDINAL
    Britons	PERSON
    Agricola	ORG
    Britain	GPE
    two	CARDINAL
    Scotland	GPE
    about forty miles	QUANTITY
    Antoninus Pius	GPE
    Antoninus	GPE
    Edinburgh	GPE
    Glasgow	PERSON
    Roman	NORP
    Caledonians	NORP
    gloomy hills	GPE
    winter	DATE
    Roman	NORP
    Imperial	ORG
    Augustus	ORG
    Trajan	ORG
    first	ORDINAL
    Trajan	PERSON
    Dacians	NORP
    Danube	PERSON
    Domitian	NORP
    Rome	GPE
    Dacian	NORP
    Trajan	GPE
    five years	DATE
    Dacia	PERSON
    second	ORDINAL
    Augustus	ORG
    about thirteen hundred miles	QUANTITY
    Dniester	ORG
    Theiss	NORP
    Tibiscus	ORG
    the Lower Danube	ORG
    the Euxine Sea	LOC
    Danube	PERSON
    Bender	PERSON
    Turkish	NORP
    Russian	NORP
    Trajan	PERSON
    Alexander	ORG
    Trajan	GPE
    Roman	NORP
    Philip	GPE
    Trajan	ORG
    Parthians	NORP
    Tigris	GPE
    Armenia	GPE
    the Persian gulf	LOC
    first	ORDINAL
    Roman	NORP
    Arabia	GPE
    Trajan	PERSON
    India	GPE
    senate	ORG
    Bosphorus	GPE
    Colchos	GPE
    Iberia	GPE
    Albania	GPE
    Osrhoene	PERSON
    Parthian	NORP
    Median	NORP
    Carduchian	NORP
    Armenia	GPE
    Mesopotamia	GPE
    Assyria	GPE
    Trajan	PERSON
    Capitol	FAC
    one	CARDINAL
    Roman	NORP
    Jupiter	LOC
    Roman	NORP
    many ages	DATE
    Terminus	ORG
    Jupiter	LOC
    Hadrian	NORP
    Trajan	PERSON
    first	ORDINAL
    Parthians	NORP
    Roman	NORP
    Armenia	GPE
    Mesopotamia	GPE
    Assyria	GPE
    Augustus	ORG
    Euphrates	LOC
    Hadrian	NORP
    Trajan	GPE
    Trajan	PERSON
    Hadrian	NORP
    Antoninus	GPE
    Caledonia	GPE
    the Upper Egypt	GPE
    Antoninus Pius	ORG
    Italy	GPE
    the twenty-three years	DATE
    Rome	GPE
    Lanuvian	NORP
    Augustus	ORG
    Hadrian	NORP
    two	CARDINAL
    Antonines	NORP
    Roman	NORP
    forty-three years	DATE
    Hadrian	NORP
    Antoninus Pius	ORG
    Roman	NORP
    Roman	NORP
    Hadrian	NORP
    Antoninus	LOC
    Parthians	NORP
    Germans	NORP
    Marcus	PERSON
    Marcus	GPE
    Euphrates	LOC
    Danube	ORG
    Roman	NORP
    Roman	NORP
    Roman	NORP
    Europe	LOC
    first	ORDINAL
    Roman	NORP
    the hour	TIME
    Roman	NORP
    Imperial	ORG
    Romans	NORP
    the evening	TIME
    daily	DATE
    winter-quarters	DATE
    Roman	NORP
    Pyrrhic	PRODUCT
    Roman	NORP
    Hadrian	NORP
    Trajan	PERSON
    Roman	NORP
    Nine centuries	DATE
    Polybius	ORG
    Punic	ORG
    Caesar	ORG
    Hadrian	NORP
    Antonines	PRODUCT
    Imperial	ORG
    ten	CARDINAL
    fifty-five	CARDINAL
    first	ORDINAL
    eleven hundred and five	CARDINAL
    nine	CARDINAL
    five hundred and fifty-five	CARDINAL
    six thousand one hundred	CARDINAL
    four feet	QUANTITY
    two and a half	DATE
    about six feet	QUANTITY
    eighteen inches	QUANTITY
    only ten or twelve	CARDINAL
    Roman	NORP
    Spanish	LANGUAGE
    eight	CARDINAL
    three feet	QUANTITY
    Greeks	PERSON
    Macedonians	NORP
    sixteen	CARDINAL
    ten	CARDINAL
    first	ORDINAL
    first	ORDINAL
    an hundred and thirty-two	CARDINAL
    nine	CARDINAL
    sixty-six	CARDINAL
    seven hundred and twenty-six	CARDINAL
    Rome	GPE
    Italy	GPE
    Trajan	PERSON
    Hadrian	NORP
    Spain	GPE
    Cappadocia	PERSON
    Roman	NORP
    East	LOC
    oblong	GPE
    Rome	GPE
    Romans	NORP
    Roman	NORP
    ten	CARDINAL
    fifty-five	CARDINAL
    Roman	NORP
    about seven hundred yards	QUANTITY
    twenty thousand	CARDINAL
    Romans	NORP
    two hundred feet	QUANTITY
    twelve feet	QUANTITY
    twelve feet	QUANTITY
    many days	DATE
    about six hours	TIME
    twenty miles	QUANTITY
    first	ORDINAL
    Roman	NORP
    six thousand eight hundred and thirty-one	DATE
    Romans	NORP
    about twelve thousand five hundred	CARDINAL
    Hadrian	NORP
    three hundred and seventy-five thousand	CARDINAL
    Romans	NORP
    Three	CARDINAL
    Britain	GPE
    Rhine	PERSON
    Danube	PERSON
    sixteen	CARDINAL
    two	CARDINAL
    Lower	LOC
    three	CARDINAL
    the Upper Germany	GPE
    one	CARDINAL
    Rhaetia	ORG
    Noricum	GPE
    four	CARDINAL
    Pannonia	GPE
    three	CARDINAL
    Maesia	GPE
    two	CARDINAL
    Dacia	PERSON
    Euphrates	ORG
    eight	CARDINAL
    six	CARDINAL
    Syria	GPE
    two	CARDINAL
    Cappadocia	PERSON
    Egypt	GPE
    Africa	LOC
    Spain	GPE
    Italy	GPE
    twenty thousand	CARDINAL
    City Cohorts and	FAC
    Praetorians	NORP
    navy	ORG
    Romans	NORP
    Tyre	ORG
    Marseilles	GPE
    Romans	NORP
    Mediterranean	LOC
    Augustus	ORG
    two	CARDINAL
    Italy	GPE
    Ravenna	PERSON
    Adriatic	NORP
    Misenum	GPE
    Naples	GPE
    two	CARDINAL
    three	CARDINAL
    Augustus	ORG
    Actium	ORG
    Liburnians	LOC
    Liburnians	NORP
    two	CARDINAL
    Ravenna	PERSON
    Misenum	GPE
    Mediterranean	LOC
    several thousand	CARDINAL
    two	CARDINAL
    Roman	NORP
    Frejus	DATE
    Provence	GPE
    Euxine	PERSON
    forty	CARDINAL
    three thousand	CARDINAL
    Gaul	PERSON
    Britain	GPE
    Rhine	PERSON
    Danube	PERSON
    Imperial	ORG
    more than four hundred and fifty thousand	CARDINAL
    the last century	DATE
    Roman	NORP
    Hadrian	NORP
    Antonines	ORG
    Spain	GPE
    Europe	LOC
    Mediterranean	LOC
    the Atlantic Ocean	LOC
    two	CARDINAL
    Augustus	ORG
    three	CARDINAL
    Lusitania	GPE
    Baetica	GPE
    Tarraconensis	ORG
    Portugal	GPE
    Lusitanians	NORP
    East	LOC
    North	LOC
    Grenada	GPE
    Andalusia	GPE
    Baetica	GPE
    Spain	GPE
    Asturias	GPE
    Biscay	GPE
    Navarre	PERSON
    Leon	PERSON
    two	CARDINAL
    Castilles	GPE
    Murcia	GPE
    Valencia	GPE
    Catalonia	GPE
    Arragon	GPE
    third	ORDINAL
    Roman	NORP
    Tarragona	GPE
    Celtiberians	NORP
    Cantabrians	NORP
    Asturians	NORP
    Rome	GPE
    first	ORDINAL
    Arabs	NORP
    Ancient Gaul	ORG
    Alps	GPE
    Rhine	GPE
    Ocean	LOC
    France	GPE
    Alsace	GPE
    Lorraine	PERSON
    Savoy	ORG
    Switzerland	GPE
    four	CARDINAL
    Rhine	GPE
    Liege	GPE
    Luxemburg	GPE
    Hainault	ORG
    Flanders	ORG
    Brabant	PERSON
    Augustus	PERSON
    Gaul	ORG
    hundred	CARDINAL
    Mediterranean	LOC
    Languedoc	GPE
    Provence	ORG
    Dauphiné	ORG
    Narbonne	PERSON
    Aquitaine	ORG
    Seine	PRODUCT
    the Celtic Gaul	LOC
    Lugdunum	GPE
    Lyons	WORK_OF_ART
    Belgic	PERSON
    Seine	ORG
    Rhine	GPE
    Caesar	ORG
    Germans	NORP
    Belgic	PERSON
    Roman	NORP
    Gallic	PERSON
    Rhine	GPE
    Basil to Leyden	PERSON
    the Lower Germany	GPE
    Antonines	NORP
    six	CARDINAL
    Gaul	ORG
    Narbonnese	NORP
    Aquitaine	ORG
    Celtic	ORG
    Lyonnese	NORP
    Belgic	ORG
    two	CARDINAL
    Britain	GPE
    Roman	NORP
    England	GPE
    Wales	GPE
    Scotland	GPE
    the Friths of Dumbarton	ORG
    Edinburgh	GPE
    Britain	GPE
    Belgae	ORG
    Brigantes	GPE
    North	LOC
    South Wales	GPE
    Iceni	NORP
    Norfolk	GPE
    Suffolk	GPE
    Spain	GPE
    Gaul	ORG
    Britain	GPE
    Roman	NORP
    European	NORP
    Hercules	GPE
    Antoninus	GPE
    Tagus	ORG
    Rhine	PERSON
    Danube	PERSON
    Roman	NORP
    Lombardy	LOC
    Italy	GPE
    Gauls	PERSON
    Po	LOC
    Piedmont	GPE
    Romagna	GPE
    Alps	LOC
    Ligurians	NORP
    Genoa	ORG
    Venice	GPE
    Adige	ORG
    Venetians	NORP
    Tuscany	PERSON
    Etruscans	NORP
    Umbrians	PERSON
    Italy	GPE
    first	ORDINAL
    Tiber	PERSON
    seven	CARDINAL
    Rome	GPE
    Sabines	ORG
    Latins	ORG
    Volsci	PRODUCT
    Naples	GPE
    first	ORDINAL
    Campania	GPE
    Naples	GPE
    Marsi	ORG
    Samnites	ORG
    Apulians	NORP
    Lucanians	NORP
    Augustus	PERSON
    Italy	GPE
    eleven	CARDINAL
    Istria	GPE
    Roman	NORP
    European	NORP
    Rome	GPE
    Rhine	PERSON
    Danube	PERSON
    only thirty miles	QUANTITY
    thirteen hundred miles	QUANTITY
    sixty	CARDINAL
    six	CARDINAL
    Euxine	PERSON
    Danube	PERSON
    Illyricum	GPE
    Illyrian	NORP
    Rhaetia, Noricum	ORG
    Pannonia	GPE
    Dalmatia	ORG
    Dacia	PERSON
    Maesia	GPE
    Thrace	GPE
    Macedonia	GPE
    Greece	GPE
    Rhaetia	ORG
    Vindelicians	NORP
    Alps	LOC
    Danube	ORG
    Inn	ORG
    Bavaria	GPE
    Augsburg	GPE
    German	NORP
    Grisons	PERSON
    Tyrol	ORG
    Austria	GPE
    Inn	ORG
    Danube	ORG
    Save	ORG
    Austria	GPE
    Styria	GPE
    Carinthia	GPE
    Carniola	ORG
    the Lower Hungary	GPE
    Sclavonia	GPE
    Noricum	GPE
    Pannonia	GPE
    Roman	NORP
    German	NORP
    Romans	NORP
    Austrian	NORP
    Bohemia	PERSON
    Moravia	GPE
    Austria	GPE
    Hungary	GPE
    Theiss	NORP
    Danube	PERSON
    Austria	GPE
    Roman	NORP
    Dalmatia	ORG
    Illyricum	GPE
    Save	ORG
    Adriatic	NORP
    Venetian	NORP
    Sclavonian	NORP
    Croatia	GPE
    Bosnia	GPE
    Austrian	NORP
    Turkish	NORP
    Christian	NORP
    Mahometan	NORP
    Danube	PERSON
    Theiss	NORP
    Save	ORG
    Greeks	WORK_OF_ART
    Ister	ORG
    Maesia	GPE
    Dacia	PERSON
    Trajan	ORG
    Danube	PERSON
    Temeswar	ORG
    Transylvania	GPE
    Hungary	GPE
    Moldavia	GPE
    Walachia	GPE
    the Ottoman Porte	GPE
    Danube	ORG
    Maesia	GPE
    the middle ages	DATE
    Servia	GPE
    Bulgaria	GPE
    Turkish	NORP
    Roumelia	PERSON
    Turks	NORP
    Thrace	GPE
    Macedonia	GPE
    Greece	GPE
    Roman	NORP
    Antonines	ORG
    Thrace	ORG
    Haemus	PERSON
    Rhodope	PERSON
    Bosphorus	PERSON
    Hellespont	GPE
    Rome	GPE
    Constantine	ORG
    Bosphorus	PERSON
    Macedonia	GPE
    Alexander	ORG
    Asia	LOC
    two	CARDINAL
    Philips	ORG
    Epirus	ORG
    Thessaly	GPE
    Aegean	NORP
    Ionian	NORP
    Thebes	GPE
    Argos	LOC
    Sparta	GPE
    Athens	GPE
    Greece	GPE
    Roman	NORP
    Achaean	NORP
    Achaia	GPE
    Europe	LOC
    Roman	NORP
    Asia	LOC
    Trajan	GPE
    Turkish	NORP
    Asia Minor	ORG
    Euxine	PERSON
    Mediterranean	LOC
    Euphrates	LOC
    Europe	LOC
    Mount Taurus	FAC
    Halys	PERSON
    Romans	NORP
    Asia	LOC
    Troy	GPE
    Lydia	ORG
    Phrygia	ORG
    Pamphylians	NORP
    Lycians	NORP
    Carians	NORP
    Grecian	NORP
    Ionia	GPE
    Bithynia	GPE
    Pontus	ORG
    Constantinople	GPE
    Trebizond	GPE
    Cilicia	PERSON
    Syria	GPE
    the Roman Asia	LOC
    Halys	PERSON
    Armenia	GPE
    Euphrates	ORG
    Cappadocia	PERSON
    Euxine	PERSON
    Trebizond	GPE
    Asia	LOC
    Danube	PERSON
    Europe	LOC
    Roman	NORP
    Crim Tartary	PERSON
    Circassia	GPE
    Mingrelia	GPE
    Alexander	GPE
    Syria	GPE
    Seleucidae	ORG
    Upper Asia	LOC
    Parthians	NORP
    Euphrates	LOC
    Mediterranean	LOC
    Syria	GPE
    Romans	NORP
    Cappadocia	PERSON
    Egypt	GPE
    the Red Sea	LOC
    Phoenicia	ORG
    Palestine	GPE
    Syria	GPE
    Wales	GPE
    Phoenicia	ORG
    Palestine	GPE
    America	GPE
    Europe	LOC
    Syria	GPE
    Euphrates	LOC
    the Red Sea	LOC
    Arabs	NORP
    Roman	NORP
    Egypt	GPE
    Africa	LOC
    Asia	LOC
    Egypt	GPE
    Roman	NORP
    Ptolemies	ORG
    Mamelukes	ORG
    Turkish	NORP
    Nile	LOC
    five hundred miles	QUANTITY
    Cancer	PERSON
    Mediterranean	LOC
    Cyrene	PERSON
    first	ORDINAL
    Greek	NORP
    Egypt	GPE
    Barca	GPE
    Cyrene	PERSON
    Africa	LOC
    above fifteen hundred miles	QUANTITY
    Mediterranean	LOC
    Sahara	LOC
    an hundred miles	QUANTITY
    Romans	NORP
    Africa	LOC
    Phoenician	NORP
    Libyans	NORP
    Tripoli	GPE
    Algiers	LOC
    Numidia	GPE
    Massinissa	PERSON
    Jugurtha	GPE
    Augustus	ORG
    Numidia	GPE
    at least two-thirds	CARDINAL
    Mauritania	GPE
    Caesariensis	PRODUCT
    Mauritania	GPE
    Moors	ORG
    Tingi	ORG
    Tangier	ORG
    Tingitana	GPE
    Sallè	NORP
    Ocean	LOC
    Romans	NORP
    Mequinez	ORG
    Morocco	GPE
    Segelmessa	PERSON
    Roman	NORP
    Africa	LOC
    Mount Atlas	ORG
    Roman	NORP
    Africa	LOC
    Spain	GPE
    about twelve miles	QUANTITY
    Atlantic	LOC
    Mediterranean	LOC
    Hercules	GPE
    two	CARDINAL
    European	NORP
    Gibraltar	LOC
    the Mediterranean Sea	LOC
    Roman	NORP
    two	CARDINAL
    Baleares	PRODUCT
    Majorca and Minorca	ORG
    Spain	GPE
    Great Britain	GPE
    Corsica	PRODUCT
    Two	CARDINAL
    Italian	NORP
    Sardinia	GPE
    Sicily	GPE
    Crete	ORG
    Candia	PERSON
    Cyprus	PRODUCT
    Greece	GPE
    Asia	LOC
    Turkish	NORP
    Malta	GPE
    Order	ORG
    Roman	NORP
    Rome	GPE
    two thousand miles	QUANTITY
    Antoninus	GPE
    Dacia	PERSON
    Mount Atlas	ORG
    Cancer	WORK_OF_ART
    more than	CARDINAL
    the Western Ocean	LOC
    Euphrates	ORG
    fifty-sixth	CARDINAL
    hundred thousand square miles	QUANTITY
    


```python
# filter by place name
for entity in first_chapter_doc.ents:
    if (entity.label_ == 'GPE') or (entity.label_ == 'LOC'):
        print(entity.text)
```

    Rome
    Nerva
    Trajan
    Marcus Antoninus
    Rome
    Rome
    Aethiopia
    Europe
    Germany
    the Atlantic Ocean
    Euphrates
    Arabia
    Africa
    Britain
    Britain
    Boadicea
    Britain
    Ireland
    Britain
    Scotland
    Antoninus Pius
    Antoninus
    Edinburgh
    gloomy hills
    Rome
    Trajan
    the Euxine Sea
    Trajan
    Philip
    Tigris
    Armenia
    the Persian gulf
    Arabia
    India
    Bosphorus
    Colchos
    Iberia
    Albania
    Armenia
    Mesopotamia
    Assyria
    Jupiter
    Jupiter
    Armenia
    Mesopotamia
    Assyria
    Euphrates
    Trajan
    Antoninus
    Caledonia
    the Upper Egypt
    Italy
    Rome
    Antoninus
    Marcus
    Euphrates
    Europe
    Rome
    Italy
    Spain
    East
    oblong
    Rome
    Britain
    Lower
    the Upper Germany
    Noricum
    Pannonia
    Maesia
    Syria
    Egypt
    Africa
    Spain
    Italy
    Marseilles
    Mediterranean
    Italy
    Misenum
    Naples
    Liburnians
    Misenum
    Mediterranean
    Provence
    Britain
    Spain
    Europe
    Mediterranean
    the Atlantic Ocean
    Lusitania
    Baetica
    Portugal
    East
    North
    Grenada
    Andalusia
    Baetica
    Spain
    Asturias
    Biscay
    Castilles
    Murcia
    Valencia
    Catalonia
    Arragon
    Tarragona
    Rome
    Alps
    Rhine
    Ocean
    France
    Alsace
    Switzerland
    Rhine
    Liege
    Luxemburg
    Mediterranean
    Languedoc
    the Celtic Gaul
    Lugdunum
    Rhine
    Rhine
    the Lower Germany
    Britain
    England
    Wales
    Scotland
    Edinburgh
    Britain
    Brigantes
    North
    South Wales
    Norfolk
    Suffolk
    Spain
    Britain
    Hercules
    Antoninus
    Lombardy
    Italy
    Po
    Piedmont
    Romagna
    Alps
    Venice
    Italy
    Rome
    Naples
    Campania
    Naples
    Italy
    Istria
    Rome
    Illyricum
    Pannonia
    Maesia
    Thrace
    Macedonia
    Greece
    Alps
    Bavaria
    Augsburg
    Austria
    Austria
    Styria
    Carinthia
    the Lower Hungary
    Sclavonia
    Noricum
    Pannonia
    Moravia
    Austria
    Hungary
    Austria
    Illyricum
    Croatia
    Bosnia
    Maesia
    Transylvania
    Hungary
    Moldavia
    Walachia
    the Ottoman Porte
    Maesia
    Servia
    Bulgaria
    Thrace
    Macedonia
    Greece
    Hellespont
    Rome
    Macedonia
    Asia
    Thessaly
    Thebes
    Argos
    Sparta
    Athens
    Greece
    Achaia
    Europe
    Asia
    Trajan
    Mediterranean
    Euphrates
    Europe
    Asia
    Troy
    Ionia
    Bithynia
    Constantinople
    Trebizond
    Syria
    the Roman Asia
    Armenia
    Trebizond
    Asia
    Europe
    Circassia
    Mingrelia
    Alexander
    Syria
    Upper Asia
    Euphrates
    Mediterranean
    Syria
    Egypt
    the Red Sea
    Palestine
    Syria
    Wales
    Palestine
    America
    Europe
    Syria
    Euphrates
    the Red Sea
    Egypt
    Africa
    Asia
    Egypt
    Nile
    Mediterranean
    Egypt
    Barca
    Africa
    Mediterranean
    Sahara
    Africa
    Tripoli
    Algiers
    Numidia
    Jugurtha
    Numidia
    Mauritania
    Mauritania
    Tingitana
    Ocean
    Morocco
    Africa
    Africa
    Spain
    Atlantic
    Mediterranean
    Hercules
    Gibraltar
    the Mediterranean Sea
    Spain
    Great Britain
    Sardinia
    Sicily
    Greece
    Asia
    Malta
    Rome
    Antoninus
    the Western Ocean
    


```python
# putting all of the data into a dictionary
import collections
place_freq = collections.defaultdict(int)
for entity in first_chapter_doc.ents:
    if (entity.label_ == 'GPE') or (entity.label_ == 'LOC'):
        place_freq[entity.text] += 1 # the utility of defaultdict!
place_freq = dict(place_freq)
```


```python
# dict -> df
place_freq_df = pd.DataFrame.from_dict(place_freq, orient='index').reset_index().rename(columns={'index':'place_name',0:'frequency'})
place_freq_df
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
      <th>place_name</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nerva</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Trajan</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Marcus Antoninus</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aethiopia</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>147</th>
      <td>Great Britain</td>
      <td>1</td>
    </tr>
    <tr>
      <th>148</th>
      <td>Sardinia</td>
      <td>1</td>
    </tr>
    <tr>
      <th>149</th>
      <td>Sicily</td>
      <td>1</td>
    </tr>
    <tr>
      <th>150</th>
      <td>Malta</td>
      <td>1</td>
    </tr>
    <tr>
      <th>151</th>
      <td>the Western Ocean</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>152 rows × 2 columns</p>
</div>




```python
# downloading the Pleiades data we need (also from my gh)
if not os.path.isfile('places.csv'):
    wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/places.csv')
if not os.path.isfile('names.csv'):
    wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/names.csv')
```

      8% [......                                                                  ]  1212416 / 13907743100% [..........................................................................] 7643851 / 7643851


```python
places = pd.read_csv('places.csv')
places

places.columns
```




    Index(['created', 'description', 'details', 'provenance', 'title', 'uri', 'id',
           'representative_latitude', 'representative_longitude',
           'bounding_box_wkt'],
          dtype='object')




```python
# let's find 'Rome' in places
places.loc[places['title'] == 'Roma']
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
      <th>created</th>
      <th>description</th>
      <th>details</th>
      <th>provenance</th>
      <th>title</th>
      <th>uri</th>
      <th>id</th>
      <th>representative_latitude</th>
      <th>representative_longitude</th>
      <th>bounding_box_wkt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>21483</th>
      <td>2018-06-07T19:48:13Z</td>
      <td>The capital of the Roman Republic and Empire.</td>
      <td>&lt;p&gt;The Barrington Atlas Directory notes: Roma/...</td>
      <td>Barrington Atlas: BAtlas 43 B2 Roma</td>
      <td>Roma</td>
      <td>https://pleiades.stoa.org/places/423025</td>
      <td>423025</td>
      <td>41.891775</td>
      <td>12.486137</td>
      <td>POLYGON ((12.486137 41.891775, 12.486137 41.89...</td>
    </tr>
  </tbody>
</table>
</div>




```python
names = pd.read_csv('names.csv')
names
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
      <th>created</th>
      <th>description</th>
      <th>details</th>
      <th>provenance</th>
      <th>title</th>
      <th>uri</th>
      <th>id</th>
      <th>place_id</th>
      <th>name_type</th>
      <th>language_tag</th>
      <th>attested_form</th>
      <th>romanized_form_1</th>
      <th>romanized_form_2</th>
      <th>romanized_form_3</th>
      <th>association_certainty</th>
      <th>transcription_accuracy</th>
      <th>transcription_completeness</th>
      <th>year_after_which</th>
      <th>year_before_which</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2010-06-24T14:11:06Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Barrington Atlas: BAtlas 1 D1 Gallia</td>
      <td>Gallia</td>
      <td>https://pleiades.stoa.org/places/993/gallia</td>
      <td>gallia</td>
      <td>993</td>
      <td>geographic</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Gallia</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2010-06-24T14:11:07Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Barrington Atlas: BAtlas 1 D1 Gallia</td>
      <td>Galli</td>
      <td>https://pleiades.stoa.org/places/993/galli</td>
      <td>galli</td>
      <td>993</td>
      <td>geographic</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Galli</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2015-06-23T17:19:22Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>House of the Ceii</td>
      <td>https://pleiades.stoa.org/places/999909607/nam...</td>
      <td>name.2015-06-23.9071239510</td>
      <td>999909607</td>
      <td>geographic</td>
      <td>en</td>
      <td>House of the Ceii</td>
      <td>House of the Ceii</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>1700.0</td>
      <td>2100.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017-06-26T23:54:15Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Casa dei Ceii</td>
      <td>https://pleiades.stoa.org/places/999909607/nam...</td>
      <td>name.2017-06-26.2201864114</td>
      <td>999909607</td>
      <td>geographic</td>
      <td>it</td>
      <td>Casa dei Ceii</td>
      <td>Casa dei Ceii</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>1700.0</td>
      <td>2100.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-12-26T17:59:50Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>House of L. Ceius Secundus</td>
      <td>https://pleiades.stoa.org/places/999909607/hou...</td>
      <td>house-of-l-ceius-secundus</td>
      <td>999909607</td>
      <td>geographic</td>
      <td>en</td>
      <td>House of L. Ceius Secundus</td>
      <td>House of L. Ceius Secundus</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>35379</th>
      <td>2020-05-14T21:59:13Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Ermanis</td>
      <td>https://pleiades.stoa.org/places/555685926/erm...</td>
      <td>ermanis</td>
      <td>555685926</td>
      <td>geographic</td>
      <td>tr</td>
      <td>Ermanis</td>
      <td>Ermanis</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>1800.0</td>
      <td>1899.0</td>
    </tr>
    <tr>
      <th>35380</th>
      <td>2021-10-29T19:58:36Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>İrmanis</td>
      <td>https://pleiades.stoa.org/places/555685926/irm...</td>
      <td>irmanis</td>
      <td>555685926</td>
      <td>geographic</td>
      <td>tr</td>
      <td>İrmanis</td>
      <td>İrmanis</td>
      <td>Irmanis</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>1800.0</td>
      <td>1899.0</td>
    </tr>
    <tr>
      <th>35381</th>
      <td>2021-10-30T10:55:10Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Gövelek</td>
      <td>https://pleiades.stoa.org/places/555685926/gov...</td>
      <td>govelek-2</td>
      <td>555685926</td>
      <td>geographic</td>
      <td>tr</td>
      <td>Gövelek</td>
      <td>Gövelek</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>2000.0</td>
      <td>2099.0</td>
    </tr>
    <tr>
      <th>35382</th>
      <td>2021-11-05T14:25:06Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>terme Eleniane</td>
      <td>https://pleiades.stoa.org/places/555157618/ter...</td>
      <td>terme-eleniane</td>
      <td>555157618</td>
      <td>geographic</td>
      <td>it</td>
      <td>terme Eleniane</td>
      <td>terme Eleniane</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>1700.0</td>
      <td>2100.0</td>
    </tr>
    <tr>
      <th>35383</th>
      <td>2021-02-14T18:25:46Z</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Pleiades</td>
      <td>Tell Ras Budran</td>
      <td>https://pleiades.stoa.org/places/555858016/tel...</td>
      <td>tell-ras-budran</td>
      <td>555858016</td>
      <td>geographic</td>
      <td>en</td>
      <td>Tell Ras Budran</td>
      <td>Tell Ras Budran</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>2000.0</td>
      <td>2099.0</td>
    </tr>
  </tbody>
</table>
<p>35384 rows × 19 columns</p>
</div>




```python
# let's find 'Rome' in names
names.loc[names['romanized_form_1'] == 'Rome']
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
      <th>created</th>
      <th>description</th>
      <th>details</th>
      <th>provenance</th>
      <th>title</th>
      <th>uri</th>
      <th>id</th>
      <th>place_id</th>
      <th>name_type</th>
      <th>language_tag</th>
      <th>attested_form</th>
      <th>romanized_form_1</th>
      <th>romanized_form_2</th>
      <th>romanized_form_3</th>
      <th>association_certainty</th>
      <th>transcription_accuracy</th>
      <th>transcription_completeness</th>
      <th>year_after_which</th>
      <th>year_before_which</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>20810</th>
      <td>2012-02-04T23:39:48Z</td>
      <td>The modern English appellation</td>
      <td>NaN</td>
      <td>Barrington Atlas: BAtlas 43 B2 Roma</td>
      <td>Rome</td>
      <td>https://pleiades.stoa.org/places/423025/rome</td>
      <td>rome</td>
      <td>423025</td>
      <td>geographic</td>
      <td>en</td>
      <td>Rome</td>
      <td>Rome</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>certain</td>
      <td>accurate</td>
      <td>complete</td>
      <td>1700.0</td>
      <td>2100.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
def get_pleiades_id(term):
    """
    Iterates through all of the possible names in the names.csv file
    Returns None if no matched names
    """
    name_row = names.loc[names['attested_form'] == term]
    if len(name_row) == 1:
        return int(name_row.place_id.iloc[0])
    else:
        name_row = names.loc[names['romanized_form_1'] == term]
        if len(name_row) == 1:
            return int(name_row.place_id.iloc[0])
        else:
            name_row = names.loc[names['romanized_form_2'] == term]
            if len(name_row) == 1:
                return int(name_row.place_id.iloc[0])
            else:
                name_row = names.loc[names['romanized_form_3'] == term]
                if len(name_row) == 1:
                    return int(name_row.place_id.iloc[0])
                else:
                    return None
```

The above function looks very complicated, but all it's doing is checking the results of several `loc` and `iloc` calls from our `DataFrame`. You will very rarely see `for` loops when using `pandas`. Instead, you will see programmers taking advantage of the very efficient search methods in `pandas` like `loc`, sometimes called 'broadcasting'. Check out this [Medium post](https://medium.com/@michaeleby1/broadcasting-versus-iteration-6c06539dc1d5) for a further discussion of the benefits of broadcasting over writing loops.


```python
place_freq_df['pleiades_id'] = place_freq_df['place_name'].apply(get_pleiades_id)
place_freq_df
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
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nerva</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Trajan</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Marcus Antoninus</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aethiopia</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>147</th>
      <td>Great Britain</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>148</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
    </tr>
    <tr>
      <th>149</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
    </tr>
    <tr>
      <th>150</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
    </tr>
    <tr>
      <th>151</th>
      <td>the Western Ocean</td>
      <td>1</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>152 rows × 3 columns</p>
</div>



Why are there so many `NaN` values? How do we deal with them? How do we want to deal with them?


```python
place_freq_final = place_freq_df.dropna().reset_index(drop=True)
place_freq_final
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
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Euphrates</td>
      <td>6</td>
      <td>912849.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabia</td>
      <td>2</td>
      <td>981506.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Africa</td>
      <td>7</td>
      <td>775.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ireland</td>
      <td>1</td>
      <td>20487.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>India</td>
      <td>1</td>
      <td>50004.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Iberia</td>
      <td>1</td>
      <td>863807.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Caledonia</td>
      <td>1</td>
      <td>89132.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Misenum</td>
      <td>2</td>
      <td>432941.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Naples</td>
      <td>3</td>
      <td>433014.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tarragona</td>
      <td>1</td>
      <td>246349.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Lugdunum</td>
      <td>1</td>
      <td>167717.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Hercules</td>
      <td>2</td>
      <td>266032.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illyricum</td>
      <td>2</td>
      <td>481865.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Macedonia</td>
      <td>3</td>
      <td>491656.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Augsburg</td>
      <td>1</td>
      <td>118580.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Thebes</td>
      <td>1</td>
      <td>541138.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Argos</td>
      <td>1</td>
      <td>570106.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sparta</td>
      <td>1</td>
      <td>570685.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Athens</td>
      <td>1</td>
      <td>579885.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Achaia</td>
      <td>1</td>
      <td>570028.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Troy</td>
      <td>1</td>
      <td>550595.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Ionia</td>
      <td>1</td>
      <td>550597.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Constantinople</td>
      <td>1</td>
      <td>520998.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Trebizond</td>
      <td>2</td>
      <td>857359.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Nile</td>
      <td>1</td>
      <td>727172.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Algiers</td>
      <td>1</td>
      <td>295276.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
    </tr>
  </tbody>
</table>
</div>



Now that we have the Pleiades IDs, we can finally get the coordiantes from `places.csv`. Let's look back at the Roman example. 


```python
rid = place_freq_final.pleiades_id.iloc[0]
type(places.loc[places['id'] == rid].representative_latitude)
```




    pandas.core.series.Series




```python
# could've just one function here, but not too much trouble to do two
def get_lat(pl_id):
    places_row = places.loc[places['id'] == pl_id]
    if len(places_row) == 1:
        return places_row.representative_latitude.iloc[0]
    
def get_long(pl_id):
    places_row = places.loc[places['id'] == pl_id]
    if len(places_row) == 1:
        return places_row.representative_longitude.iloc[0]
```


```python
place_freq_final['lat'] = place_freq_final['pleiades_id'].apply(get_lat)
place_freq_final['long'] = place_freq_final['pleiades_id'].apply(get_long)
```


```python
place_freq_final
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
      <th>place_name</th>
      <th>frequency</th>
      <th>pleiades_id</th>
      <th>lat</th>
      <th>long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rome</td>
      <td>12</td>
      <td>423025.0</td>
      <td>41.891775</td>
      <td>12.486137</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Euphrates</td>
      <td>6</td>
      <td>912849.0</td>
      <td>35.543310</td>
      <td>39.606018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arabia</td>
      <td>2</td>
      <td>981506.0</td>
      <td>27.500000</td>
      <td>32.500000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Africa</td>
      <td>7</td>
      <td>775.0</td>
      <td>32.500000</td>
      <td>7.500000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ireland</td>
      <td>1</td>
      <td>20487.0</td>
      <td>53.184028</td>
      <td>-7.717526</td>
    </tr>
    <tr>
      <th>5</th>
      <td>India</td>
      <td>1</td>
      <td>50004.0</td>
      <td>22.500000</td>
      <td>77.500000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Iberia</td>
      <td>1</td>
      <td>863807.0</td>
      <td>41.836468</td>
      <td>44.689138</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Caledonia</td>
      <td>1</td>
      <td>89132.0</td>
      <td>57.500000</td>
      <td>-4.500000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Misenum</td>
      <td>2</td>
      <td>432941.0</td>
      <td>40.786279</td>
      <td>14.084884</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Naples</td>
      <td>3</td>
      <td>433014.0</td>
      <td>40.839995</td>
      <td>14.252870</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tarragona</td>
      <td>1</td>
      <td>246349.0</td>
      <td>41.116892</td>
      <td>1.258338</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Lugdunum</td>
      <td>1</td>
      <td>167717.0</td>
      <td>45.758866</td>
      <td>4.819482</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Hercules</td>
      <td>2</td>
      <td>266032.0</td>
      <td>37.500000</td>
      <td>-0.500000</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Illyricum</td>
      <td>2</td>
      <td>481865.0</td>
      <td>42.427292</td>
      <td>17.965028</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Macedonia</td>
      <td>3</td>
      <td>491656.0</td>
      <td>41.250000</td>
      <td>21.750000</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Augsburg</td>
      <td>1</td>
      <td>118580.0</td>
      <td>48.365463</td>
      <td>10.894765</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Thebes</td>
      <td>1</td>
      <td>541138.0</td>
      <td>38.319156</td>
      <td>23.317577</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Argos</td>
      <td>1</td>
      <td>570106.0</td>
      <td>37.631561</td>
      <td>22.719464</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Sparta</td>
      <td>1</td>
      <td>570685.0</td>
      <td>37.077905</td>
      <td>22.427298</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Athens</td>
      <td>1</td>
      <td>579885.0</td>
      <td>37.972634</td>
      <td>23.722746</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Achaia</td>
      <td>1</td>
      <td>570028.0</td>
      <td>38.102121</td>
      <td>22.224586</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Troy</td>
      <td>1</td>
      <td>550595.0</td>
      <td>39.957433</td>
      <td>26.238459</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Ionia</td>
      <td>1</td>
      <td>550597.0</td>
      <td>38.162194</td>
      <td>27.357498</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Constantinople</td>
      <td>1</td>
      <td>520998.0</td>
      <td>41.006371</td>
      <td>28.965352</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Trebizond</td>
      <td>2</td>
      <td>857359.0</td>
      <td>41.004269</td>
      <td>39.723312</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Nile</td>
      <td>1</td>
      <td>727172.0</td>
      <td>19.211409</td>
      <td>30.567330</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Algiers</td>
      <td>1</td>
      <td>295276.0</td>
      <td>36.768912</td>
      <td>3.053218</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Sardinia</td>
      <td>1</td>
      <td>472014.0</td>
      <td>39.871087</td>
      <td>8.957517</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Sicily</td>
      <td>1</td>
      <td>462492.0</td>
      <td>37.643297</td>
      <td>13.932018</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Malta</td>
      <td>1</td>
      <td>462311.0</td>
      <td>35.908887</td>
      <td>14.408042</td>
    </tr>
  </tbody>
</table>
</div>




```python
place_freq_final.to_csv('ch1gibbon_places.csv')
```

## In-class Activity

Now pair up and try to take all of the above steps and turn it into a single function, so that all you need to do is pass in the text of a chapter and you get back a DataFrame with place names and coordinates. To walkthrough the steps again:

* Consider what all of your inputs are
* Use `spaCy` syntax to parse input text
* Filter and count by place name and label
* Use `pandas` to manipulate the data into a useful form

Try out some different chapters. Do you notice anything about the places? Do the types of places or the accuracy of Pleiades get better or worse over the chapters? I encourage you to Google or look up any entities you don't know on Wikipedia.


```python
def find_NER(chapter=gibbon_by_chapter['StringText'][1]):
    second_chapter = chapter
    second_chapter_doc = nlp(second_chapter)
    import collections
    place_freq = collections.defaultdict(int)
    for entity in second_chapter_doc.ents:
        if (entity.label_ == 'GPE') or (entity.label_ == 'LOC'):
            place_freq[entity.text] += 1 # the utility of defaultdict!
    place_freq = dict(place_freq)
    place_freq_df = pd.DataFrame.from_dict(place_freq, orient='index').reset_index().rename(columns={'index':'place_name',0:'frequency'})
    place_freq_df
   
    if not os.path.isfile('places.csv'):
        wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/places.csv')
    if not os.path.isfile('names.csv'):
        wget.download('https://raw.githubusercontent.com/pnadelofficial/FallDHCourseMaterials/main/names.csv')

    places = pd.read_csv('places.csv')
    names = pd.read_csv('names.csv')
    #get_pleiades_id(term)
    place_freq_df['pleiades_id'] = place_freq_df['place_name'].apply(get_pleiades_id)
    place_freq_final = place_freq_df.dropna().reset_index(drop=True)
    rid = place_freq_final.pleiades_id.iloc[0]
   
    place_freq_final['lat'] = place_freq_final['pleiades_id'].apply(get_lat)
    place_freq_final['long'] = place_freq_final['pleiades_id'].apply(get_long)
    print(place_freq_final)
    place_freq_final.to_csv('ch2gibbon_places.csv')
    
```


```python
find_NER()
```

       place_name  frequency  pleiades_id        lat       long
    0        Rome         32     423025.0  41.891775  12.486137
    1        Nile          4     727172.0  19.211409  30.567330
    2      Athens         11     579885.0  37.972634  23.722746
    3      Sparta          1     570685.0  37.077905  22.427298
    4       Padua          2     393473.0  45.409561  11.876975
    5     Arpinum          1     432700.0  41.648422  13.609876
    6     Etruria          1     413122.0  42.758127  11.546721
    7      Latium          2     432900.0  41.590543  13.192265
    8      Alesia          1     177434.0  47.536622   4.503884
    9      Africa          4        775.0  32.500000   7.500000
    10  Euphrates          1     912849.0  35.543310  39.606018
    11      Capua          1     432754.0  41.086092  14.250207
    12     Verona          2     383816.0  45.442130  10.995736
    13      Troas          1     550944.0  39.837052  26.336944
    14      Milan          1     383706.0  45.463746   9.188060
    15     London          1      79574.0  51.513335  -0.088949
    16      Paris          1     109126.0  48.854405   2.346168
    17      Arles          1     148217.0  43.677616   4.630799
    18   Bordeaux          1     138248.0  44.837205  -0.576533
    19      Autun          1     177460.0  46.947240   4.299177
    20     Vienne          1     167719.0  45.524510   4.875982
    21   Hercules          1     266032.0  37.500000  -0.500000
    22     Sicily          1     462492.0  37.643297  13.932018
    23      Media          1     903080.0  34.500000  46.500000
    24     Arabia          2     981506.0  27.500000  32.500000
    25      India          2      50004.0  22.500000  77.500000
    


```python

```
