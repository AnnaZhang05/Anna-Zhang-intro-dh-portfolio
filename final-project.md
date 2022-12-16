# Introduction
This is my final project on analyzing the Hou Han Shu from Han dynasty of ancient China using Stanza treebank parsing, Ugarit translation alighment, and source translation from Frederich Hirth's book China and the Roman Orient: researches into their ancient and mediaeval relations as represented in old Chinese records. In this project, I specifically target Volume 88: Treatise on the Western Regions, since its within my interests of comparative history between ancient China and the west. I chose the fourth paragrpah from the passage detailing "Daqin guo," the Classical Chinese name for ancient Rome, specifically for  its details on international trade and foreign interactions between Han dynasty China and different empires relating to Rome. 

# Ugarit Alignment
Here is the Ugarit alignment between the original Classical Chinese text and Hirth's English translation that I completed. This is my first time reading and analyzing Classical Chinese text in an academic setting, which proved to be a learning curve that I was able to cross over with the help of the English translation as well. In other words, I used both the original Classical Chinese and English to build and perfect my understanding of the Classical Chinese for this alignment portion.
### https://ugarit.ialigner.com/text.php?id=34115

# Stanza parsing

## Ancient Chinese Analysis

See 
* https://stanfordnlp.github.io/stanza/depparse.html
* https://universaldependencies.org/u/dep/


```python
import stanza
```


```python
stanza.download('lzh')
stanza.download('en')
```


    Downloading https://raw.githubusercontent.com/stanfordnlp/stanza-resources/main/resources_1.4.1.json:   0%|   …


    2022-12-09 10:47:13 INFO: Downloading default packages for language: lzh (Classical_Chinese) ...
    2022-12-09 10:47:14 INFO: File exists: C:\Users\17742\stanza_resources\lzh\default.zip
    2022-12-09 10:47:16 INFO: Finished downloading models and saved to C:\Users\17742\stanza_resources.
    


    Downloading https://raw.githubusercontent.com/stanfordnlp/stanza-resources/main/resources_1.4.1.json:   0%|   …


    2022-12-09 10:47:17 INFO: Downloading default packages for language: en (English) ...
    2022-12-09 10:47:19 INFO: File exists: C:\Users\17742\stanza_resources\en\default.zip
    2022-12-09 10:47:30 INFO: Finished downloading models and saved to C:\Users\17742\stanza_resources.
    


```python
nlp = stanza.Pipeline(lang='lzh', processors='tokenize,pos,lemma,depparse')
nlp_en = stanza.Pipeline(lang='en', processors= 'tokenize,pos,lemma,depparse')
```

    2022-12-13 11:15:56 INFO: Checking for updates to resources.json in case models have been updated.  Note: this behavior can be turned off with download_method=None or download_method=DownloadMethod.REUSE_RESOURCES
    


    Downloading https://raw.githubusercontent.com/stanfordnlp/stanza-resources/main/resources_1.4.1.json:   0%|   …


    2022-12-13 11:15:57 INFO: Loading these models for language: lzh (Classical_Chinese):
    =======================
    | Processor | Package |
    -----------------------
    | tokenize  | kyoto   |
    | pos       | kyoto   |
    | lemma     | kyoto   |
    | depparse  | kyoto   |
    =======================
    
    2022-12-13 11:15:57 INFO: Use device: cpu
    2022-12-13 11:15:57 INFO: Loading: tokenize
    2022-12-13 11:15:57 INFO: Loading: pos
    2022-12-13 11:15:57 INFO: Loading: lemma
    2022-12-13 11:15:57 INFO: Loading: depparse
    2022-12-13 11:15:57 INFO: Done loading processors!
    2022-12-13 11:15:57 INFO: Checking for updates to resources.json in case models have been updated.  Note: this behavior can be turned off with download_method=None or download_method=DownloadMethod.REUSE_RESOURCES
    


    Downloading https://raw.githubusercontent.com/stanfordnlp/stanza-resources/main/resources_1.4.1.json:   0%|   …


    2022-12-13 11:15:59 INFO: Loading these models for language: en (English):
    ========================
    | Processor | Package  |
    ------------------------
    | tokenize  | combined |
    | pos       | combined |
    | lemma     | combined |
    | depparse  | combined |
    ========================
    
    2022-12-13 11:15:59 INFO: Use device: cpu
    2022-12-13 11:15:59 INFO: Loading: tokenize
    2022-12-13 11:15:59 INFO: Loading: pos
    2022-12-13 11:15:59 INFO: Loading: lemma
    2022-12-13 11:15:59 INFO: Loading: depparse
    2022-12-13 11:16:00 INFO: Done loading processors!
    


```python
doc = nlp('以金银為钱，银钱十当金钱一。與安息、天竺交市于海中，利有十倍。其人質直，市无二价。谷食常贱，國用富饶。邻國使到其界首者，乘驿詣王都，至則给以金钱。其王常欲通使于汉，而安息欲以汉缯彩與之交市，故遮阂不得自达。至桓帝延熹九年，大秦王安敦遣使自日南徼外獻象牙、犀角、玳瑁，始乃一通焉。其所表貢，并无珍異，疑傳者过焉。')
```


```python
for sent in doc.sentences:
    for word in sent.words:
        print(f'word: {word.text} | ' 
              f'head word: {sent.words[word.head-1].text if word.head > 0 else "root"} |  '
              f'dependency relationship: {word.deprel}')
```

    word: 以 | head word: 為 |  dependency relationship: advcl
    word: 金 | head word: 以 |  dependency relationship: obj
    word: 银 | head word: 金 |  dependency relationship: conj
    word: 為 | head word: root |  dependency relationship: root
    word: 钱 | head word: 為 |  dependency relationship: obj
    word: ， | head word: 银 |  dependency relationship: nmod
    word: 银 | head word: 交 |  dependency relationship: nsubj
    word: 钱十 | head word: 银 |  dependency relationship: nummod
    word: 当 | head word: 银 |  dependency relationship: conj
    word: 金 | head word: 钱 |  dependency relationship: nmod
    word: 钱 | head word: 银 |  dependency relationship: conj
    word: 一 | head word: 钱 |  dependency relationship: nummod
    word: 。 | head word: 一 |  dependency relationship: clf
    word: 與 | head word: 、 |  dependency relationship: cc
    word: 安 | head word: 、 |  dependency relationship: amod
    word: 息 | head word: 安 |  dependency relationship: flat:vv
    word: 、 | head word: 银 |  dependency relationship: conj
    word: 天 | head word: 竺 |  dependency relationship: nmod
    word: 竺 | head word: 交 |  dependency relationship: nsubj
    word: 交 | head word: root |  dependency relationship: root
    word: 市 | head word: 交 |  dependency relationship: obj
    word: 于 | head word: 中 |  dependency relationship: case
    word: 海 | head word: 中 |  dependency relationship: nmod
    word: 中 | head word: 交 |  dependency relationship: obl:lmod
    word: ， | head word: 有 |  dependency relationship: advmod
    word: 利 | head word: 有 |  dependency relationship: nsubj
    word: 有 | head word: root |  dependency relationship: root
    word: 十 | head word: 倍 |  dependency relationship: nummod
    word: 倍 | head word: 有 |  dependency relationship: obj
    word: 。 | head word: 有 |  dependency relationship: ccomp
    word: 其 | head word: 人 |  dependency relationship: det
    word: 人 | head word: 質 |  dependency relationship: nsubj
    word: 質 | head word: 。 |  dependency relationship: ccomp
    word: 直 | head word: 質 |  dependency relationship: flat:vv
    word: ， | head word: 質 |  dependency relationship: conj
    word: 市 | head word: 无 |  dependency relationship: nmod
    word: 无 | head word: ， |  dependency relationship: conj
    word: 二 | head word: 价。谷 |  dependency relationship: nummod
    word: 价。谷 | head word: 食 |  dependency relationship: nmod
    word: 食 | head word: 无 |  dependency relationship: conj
    word: 常 | head word: 贱 |  dependency relationship: nmod
    word: 贱 | head word: 食 |  dependency relationship: conj
    word: ， | head word: 國 |  dependency relationship: nmod
    word: 國 | head word: 贱 |  dependency relationship: nmod
    word: 用 | head word: 國 |  dependency relationship: amod
    word: 富 | head word: 用 |  dependency relationship: obj
    word: 饶 | head word: 富 |  dependency relationship: conj
    word: 。 | head word: 用 |  dependency relationship: parataxis
    word: 邻 | head word: 國 |  dependency relationship: nmod
    word: 國 | head word: 。 |  dependency relationship: obj
    word: 使 | head word: root |  dependency relationship: root
    word: 到 | head word: 使 |  dependency relationship: ccomp
    word: 其 | head word: 界 |  dependency relationship: det
    word: 界 | head word: 到 |  dependency relationship: obj
    word: 首 | head word: 者 |  dependency relationship: amod
    word: 者 | head word: 乘 |  dependency relationship: nsubj
    word: ， | head word: 乘 |  dependency relationship: nummod
    word: 乘 | head word: root |  dependency relationship: root
    word: 驿 | head word: 乘 |  dependency relationship: obj
    word: 詣 | head word: 乘 |  dependency relationship: parataxis
    word: 王 | head word: 都 |  dependency relationship: nmod
    word: 都 | head word: 詣 |  dependency relationship: obj
    word: ， | head word: 至 |  dependency relationship: advmod
    word: 至 | head word: 詣 |  dependency relationship: parataxis
    word: 則 | head word: 。 |  dependency relationship: advmod
    word: 给 | head word: 。 |  dependency relationship: nsubj
    word: 以 | head word: 。 |  dependency relationship: advcl
    word: 金 | head word: 以 |  dependency relationship: obj
    word: 钱 | head word: 金 |  dependency relationship: conj
    word: 。 | head word: 通 |  dependency relationship: csubj
    word: 其 | head word: 王 |  dependency relationship: det
    word: 王 | head word: 通 |  dependency relationship: nsubj
    word: 常 | head word: 通 |  dependency relationship: advmod
    word: 欲 | head word: 通 |  dependency relationship: aux
    word: 通 | head word: root |  dependency relationship: root
    word: 使 | head word: 通 |  dependency relationship: flat:vv
    word: 于 | head word: ， |  dependency relationship: case
    word: 汉 | head word: ， |  dependency relationship: nmod
    word: ， | head word: 使 |  dependency relationship: obl:lmod
    word: 而 | head word: 安 |  dependency relationship: cc
    word: 安 | head word: 使 |  dependency relationship: conj
    word: 息 | head word: 安 |  dependency relationship: flat:vv
    word: 欲 | head word: 交 |  dependency relationship: aux
    word: 以 | head word: 交 |  dependency relationship: advcl
    word: 汉 | head word: 以 |  dependency relationship: obj
    word: 缯 | head word: 汉 |  dependency relationship: conj
    word: 彩 | head word: 汉 |  dependency relationship: conj
    word: 與 | head word: 之 |  dependency relationship: case
    word: 之 | head word: 交 |  dependency relationship: obl
    word: 交 | head word: root |  dependency relationship: root
    word: 市 | head word: 交 |  dependency relationship: obj
    word: ， | head word: 故 |  dependency relationship: det
    word: 故 | head word: 遮 |  dependency relationship: advcl
    word: 遮 | head word: 遣 |  dependency relationship: advcl
    word: 阂 | head word: 遮 |  dependency relationship: obj
    word: 不 | head word: 得 |  dependency relationship: advmod
    word: 得 | head word: 遮 |  dependency relationship: conj
    word: 自 | head word: 。 |  dependency relationship: case
    word: 达 | head word: 。 |  dependency relationship: nmod
    word: 。 | head word: 至 |  dependency relationship: obl
    word: 至 | head word: 遮 |  dependency relationship: conj
    word: 桓 | head word: 帝 |  dependency relationship: compound
    word: 帝 | head word: 至 |  dependency relationship: obj
    word: 延熹 | head word: 九 |  dependency relationship: nmod
    word: 九 | head word: 帝 |  dependency relationship: nummod
    word: 年 | head word: 九 |  dependency relationship: clf
    word: ， | head word: 九 |  dependency relationship: nmod
    word: 大 | head word: 王 |  dependency relationship: amod
    word: 秦 | head word: 王 |  dependency relationship: nmod
    word: 王 | head word: 遣 |  dependency relationship: nsubj
    word: 安 | head word: 王 |  dependency relationship: flat
    word: 敦 | head word: 王 |  dependency relationship: flat
    word: 遣 | head word: root |  dependency relationship: root
    word: 使 | head word: 遣 |  dependency relationship: obj
    word: 自 | head word: 日 |  dependency relationship: case
    word: 日 | head word: 徼 |  dependency relationship: obl:tmod
    word: 南 | head word: 徼 |  dependency relationship: obl:lmod
    word: 徼 | head word: 遣 |  dependency relationship: xcomp
    word: 外 | head word: 徼 |  dependency relationship: obl:lmod
    word: 獻 | head word: 徼 |  dependency relationship: parataxis
    word: 象 | head word: 牙 |  dependency relationship: nmod
    word: 牙 | head word: 獻 |  dependency relationship: obj
    word: 、 | head word: 通 |  dependency relationship: nsubj
    word: 犀 | head word: 、 |  dependency relationship: conj
    word: 角 | head word: 、 |  dependency relationship: conj
    word: 、 | head word: 、 |  dependency relationship: conj
    word: 玳 | head word: 、 |  dependency relationship: conj
    word: 瑁 | head word: 、 |  dependency relationship: conj
    word: ， | head word: 、 |  dependency relationship: conj
    word: 始 | head word: 通 |  dependency relationship: advmod
    word: 乃 | head word: 通 |  dependency relationship: advmod
    word: 一 | head word: 通 |  dependency relationship: nummod
    word: 通 | head word: root |  dependency relationship: root
    word: 焉 | head word: 通 |  dependency relationship: discourse:sp
    word: 。 | head word: root |  dependency relationship: root
    word: 其 | head word: 表 |  dependency relationship: nsubj
    word: 所 | head word: 表 |  dependency relationship: mark
    word: 表 | head word: 。 |  dependency relationship: ccomp
    word: 貢 | head word: 者 |  dependency relationship: acl
    word: ， | head word: 貢 |  dependency relationship: conj
    word: 并 | head word: 无 |  dependency relationship: advmod
    word: 无 | head word: 者 |  dependency relationship: acl
    word: 珍 | head word: 无 |  dependency relationship: flat:vv
    word: 異 | head word: 貢 |  dependency relationship: conj
    word: ， | head word: 異 |  dependency relationship: flat:vv
    word: 疑 | head word: 无 |  dependency relationship: conj
    word: 傳 | head word: 疑 |  dependency relationship: flat:vv
    word: 者 | head word: 过 |  dependency relationship: nsubj
    word: 过 | head word: root |  dependency relationship: root
    word: 焉 | head word: 过 |  dependency relationship: discourse:sp
    word: 。 | head word: 过 |  dependency relationship: discourse:sp
    

## English Translation Analysis


```python
translation= nlp_en("They make coins of gold and silver. Ten units of silver are worth one of gold. They traffic by sea with An-hsi (Parthia) and T'ien-chu [India], the profit of which trade is ten-fold. They are honest in their transactions, and there are no double prices. Cereals are always cheap. The budget is based on a wellfilled treasury. When the embassies of neighbouring countries come to their frontier, they are driven by post to the capital, and, on arrival, are presented with golden money. Their kings always desired to send embassies to China, but the An-hsi [Parthians] wished to carry on trade with them in Chinese silks, and it is for this reason that they were cut off from cominunication. This lasted till the ninth year of the Yen-hsi period during the emperor Huan-ti's reign (A.D. 166) when the king of Ta-ts'in, An-tun, sent an embassy who, from the frontier of Jih-nan (Annam) offered ivory, rhinoceros horns, and tortoise shell. From that time dates the direct intercourse with this country. The list of their tribute contained no jewels whatever, which fact throws doubt on the tradition. ")
```


```python
for sent in translation.sentences:
    for word in sent.words:
        print(f'word: {word.text} | ' 
              f'head word: {sent.words[word.head-1].text if word.head > 0 else "root"} |  '
              f'dependency relationship: {word.deprel}')
```

    word: They | head word: make |  dependency relationship: nsubj
    word: make | head word: root |  dependency relationship: root
    word: coins | head word: make |  dependency relationship: obj
    word: of | head word: gold |  dependency relationship: case
    word: gold | head word: coins |  dependency relationship: nmod
    word: and | head word: silver |  dependency relationship: cc
    word: silver | head word: gold |  dependency relationship: conj
    word: . | head word: make |  dependency relationship: punct
    word: Ten | head word: units |  dependency relationship: nummod
    word: units | head word: worth |  dependency relationship: nsubj
    word: of | head word: silver |  dependency relationship: case
    word: silver | head word: units |  dependency relationship: nmod
    word: are | head word: worth |  dependency relationship: cop
    word: worth | head word: root |  dependency relationship: root
    word: one | head word: worth |  dependency relationship: obj
    word: of | head word: gold |  dependency relationship: case
    word: gold | head word: one |  dependency relationship: nmod
    word: . | head word: worth |  dependency relationship: punct
    word: They | head word: traffic |  dependency relationship: nsubj
    word: traffic | head word: root |  dependency relationship: root
    word: by | head word: sea |  dependency relationship: case
    word: sea | head word: traffic |  dependency relationship: obl
    word: with | head word: An |  dependency relationship: case
    word: An | head word: traffic |  dependency relationship: obl
    word: - | head word: hsi |  dependency relationship: punct
    word: hsi | head word: An |  dependency relationship: flat
    word: ( | head word: Parthia |  dependency relationship: punct
    word: Parthia | head word: hsi |  dependency relationship: appos
    word: ) | head word: Parthia |  dependency relationship: punct
    word: and | head word: T'ien |  dependency relationship: cc
    word: T'ien | head word: An |  dependency relationship: conj
    word: - | head word: chu |  dependency relationship: punct
    word: chu | head word: T'ien |  dependency relationship: flat
    word: [ | head word: India |  dependency relationship: punct
    word: India | head word: T'ien |  dependency relationship: appos
    word: ] | head word: India |  dependency relationship: punct
    word: , | head word: profit |  dependency relationship: punct
    word: the | head word: profit |  dependency relationship: det
    word: profit | head word: T'ien |  dependency relationship: appos
    word: of | head word: fold |  dependency relationship: mark
    word: which | head word: trade |  dependency relationship: det
    word: trade | head word: fold |  dependency relationship: nsubj
    word: is | head word: fold |  dependency relationship: cop
    word: ten | head word: fold |  dependency relationship: nummod
    word: - | head word: fold |  dependency relationship: punct
    word: fold | head word: profit |  dependency relationship: acl:relcl
    word: . | head word: traffic |  dependency relationship: punct
    word: They | head word: honest |  dependency relationship: nsubj
    word: are | head word: honest |  dependency relationship: cop
    word: honest | head word: root |  dependency relationship: root
    word: in | head word: transactions |  dependency relationship: case
    word: their | head word: transactions |  dependency relationship: nmod:poss
    word: transactions | head word: honest |  dependency relationship: obl
    word: , | head word: are |  dependency relationship: punct
    word: and | head word: are |  dependency relationship: cc
    word: there | head word: are |  dependency relationship: expl
    word: are | head word: honest |  dependency relationship: conj
    word: no | head word: prices |  dependency relationship: det
    word: double | head word: prices |  dependency relationship: amod
    word: prices | head word: are |  dependency relationship: nsubj
    word: . | head word: honest |  dependency relationship: punct
    word: Cereals | head word: cheap |  dependency relationship: nsubj
    word: are | head word: cheap |  dependency relationship: cop
    word: always | head word: cheap |  dependency relationship: advmod
    word: cheap | head word: root |  dependency relationship: root
    word: . | head word: cheap |  dependency relationship: punct
    word: The | head word: budget |  dependency relationship: det
    word: budget | head word: based |  dependency relationship: nsubj:pass
    word: is | head word: based |  dependency relationship: aux:pass
    word: based | head word: root |  dependency relationship: root
    word: on | head word: treasury |  dependency relationship: case
    word: a | head word: treasury |  dependency relationship: det
    word: wellfilled | head word: treasury |  dependency relationship: amod
    word: treasury | head word: based |  dependency relationship: obl
    word: . | head word: based |  dependency relationship: punct
    word: When | head word: come |  dependency relationship: mark
    word: the | head word: embassies |  dependency relationship: det
    word: embassies | head word: come |  dependency relationship: nsubj
    word: of | head word: countries |  dependency relationship: case
    word: neighbouring | head word: countries |  dependency relationship: amod
    word: countries | head word: embassies |  dependency relationship: nmod
    word: come | head word: driven |  dependency relationship: advcl
    word: to | head word: frontier |  dependency relationship: case
    word: their | head word: frontier |  dependency relationship: nmod:poss
    word: frontier | head word: come |  dependency relationship: obl
    word: , | head word: come |  dependency relationship: punct
    word: they | head word: driven |  dependency relationship: nsubj:pass
    word: are | head word: driven |  dependency relationship: aux:pass
    word: driven | head word: root |  dependency relationship: root
    word: by | head word: post |  dependency relationship: case
    word: post | head word: driven |  dependency relationship: obl
    word: to | head word: capital |  dependency relationship: case
    word: the | head word: capital |  dependency relationship: det
    word: capital | head word: post |  dependency relationship: nmod
    word: , | head word: presented |  dependency relationship: punct
    word: and | head word: presented |  dependency relationship: cc
    word: , | head word: and |  dependency relationship: punct
    word: on | head word: arrival |  dependency relationship: case
    word: arrival | head word: presented |  dependency relationship: obl
    word: , | head word: arrival |  dependency relationship: punct
    word: are | head word: presented |  dependency relationship: aux:pass
    word: presented | head word: driven |  dependency relationship: conj
    word: with | head word: money |  dependency relationship: case
    word: golden | head word: money |  dependency relationship: amod
    word: money | head word: presented |  dependency relationship: obl
    word: . | head word: driven |  dependency relationship: punct
    word: Their | head word: kings |  dependency relationship: nmod:poss
    word: kings | head word: desired |  dependency relationship: nsubj
    word: always | head word: desired |  dependency relationship: advmod
    word: desired | head word: root |  dependency relationship: root
    word: to | head word: send |  dependency relationship: mark
    word: send | head word: desired |  dependency relationship: xcomp
    word: embassies | head word: send |  dependency relationship: obj
    word: to | head word: China |  dependency relationship: case
    word: China | head word: send |  dependency relationship: obl
    word: , | head word: wished |  dependency relationship: punct
    word: but | head word: wished |  dependency relationship: cc
    word: the | head word: hsi |  dependency relationship: det
    word: An- | head word: hsi |  dependency relationship: compound
    word: hsi | head word: wished |  dependency relationship: nsubj
    word: [ | head word: Parthians |  dependency relationship: punct
    word: Parthians | head word: hsi |  dependency relationship: appos
    word: ] | head word: Parthians |  dependency relationship: punct
    word: wished | head word: desired |  dependency relationship: conj
    word: to | head word: carry |  dependency relationship: mark
    word: carry | head word: wished |  dependency relationship: xcomp
    word: on | head word: trade |  dependency relationship: case
    word: trade | head word: carry |  dependency relationship: obj
    word: with | head word: them |  dependency relationship: case
    word: them | head word: carry |  dependency relationship: obl
    word: in | head word: silks |  dependency relationship: case
    word: Chinese | head word: silks |  dependency relationship: amod
    word: silks | head word: carry |  dependency relationship: obl
    word: , | head word: is |  dependency relationship: punct
    word: and | head word: is |  dependency relationship: cc
    word: it | head word: is |  dependency relationship: expl
    word: is | head word: desired |  dependency relationship: conj
    word: for | head word: reason |  dependency relationship: case
    word: this | head word: reason |  dependency relationship: det
    word: reason | head word: is |  dependency relationship: obl
    word: that | head word: cut |  dependency relationship: mark
    word: they | head word: cut |  dependency relationship: nsubj:pass
    word: were | head word: cut |  dependency relationship: aux:pass
    word: cut | head word: reason |  dependency relationship: acl
    word: off | head word: cut |  dependency relationship: compound:prt
    word: from | head word: cominunication |  dependency relationship: case
    word: cominunication | head word: cut |  dependency relationship: obl
    word: . | head word: desired |  dependency relationship: punct
    word: This | head word: lasted |  dependency relationship: nsubj
    word: lasted | head word: root |  dependency relationship: root
    word: till | head word: year |  dependency relationship: case
    word: the | head word: year |  dependency relationship: det
    word: ninth | head word: year |  dependency relationship: amod
    word: year | head word: lasted |  dependency relationship: obl
    word: of | head word: period |  dependency relationship: case
    word: the | head word: period |  dependency relationship: det
    word: Yen | head word: hsi |  dependency relationship: compound
    word: - | head word: hsi |  dependency relationship: punct
    word: hsi | head word: period |  dependency relationship: compound
    word: period | head word: year |  dependency relationship: nmod
    word: during | head word: emperor |  dependency relationship: case
    word: the | head word: emperor |  dependency relationship: det
    word: emperor | head word: lasted |  dependency relationship: obl
    word: Huan | head word: emperor |  dependency relationship: appos
    word: - | head word: ti's |  dependency relationship: punct
    word: ti's | head word: Huan |  dependency relationship: flat
    word: reign | head word: emperor |  dependency relationship: appos
    word: ( | head word: A.D. |  dependency relationship: punct
    word: A.D. | head word: emperor |  dependency relationship: appos
    word: 166 | head word: A.D. |  dependency relationship: dep
    word: ) | head word: A.D. |  dependency relationship: punct
    word: when | head word: sent |  dependency relationship: mark
    word: the | head word: king |  dependency relationship: det
    word: king | head word: sent |  dependency relationship: nsubj
    word: of | head word: Ta-ts |  dependency relationship: case
    word: Ta-ts | head word: king |  dependency relationship: nmod
    word: 'in | head word: Ta-ts |  dependency relationship: flat
    word: , | head word: tun |  dependency relationship: punct
    word: An | head word: tun |  dependency relationship: det
    word: - | head word: An |  dependency relationship: punct
    word: tun | head word: Ta-ts |  dependency relationship: appos
    word: , | head word: king |  dependency relationship: punct
    word: sent | head word: lasted |  dependency relationship: advcl
    word: an | head word: embassy |  dependency relationship: det
    word: embassy | head word: sent |  dependency relationship: obj
    word: who | head word: offered |  dependency relationship: nsubj
    word: , | head word: who |  dependency relationship: punct
    word: from | head word: frontier |  dependency relationship: case
    word: the | head word: frontier |  dependency relationship: det
    word: frontier | head word: offered |  dependency relationship: obl
    word: of | head word: Jih-nan |  dependency relationship: case
    word: Jih-nan | head word: frontier |  dependency relationship: nmod
    word: ( | head word: Annam |  dependency relationship: punct
    word: Annam | head word: Jih-nan |  dependency relationship: appos
    word: ) | head word: Annam |  dependency relationship: punct
    word: offered | head word: embassy |  dependency relationship: acl:relcl
    word: ivory | head word: offered |  dependency relationship: obj
    word: , | head word: horns |  dependency relationship: punct
    word: rhinoceros | head word: horns |  dependency relationship: compound
    word: horns | head word: ivory |  dependency relationship: conj
    word: , | head word: shell |  dependency relationship: punct
    word: and | head word: shell |  dependency relationship: cc
    word: tortoise | head word: shell |  dependency relationship: compound
    word: shell | head word: ivory |  dependency relationship: conj
    word: . | head word: lasted |  dependency relationship: punct
    word: From | head word: time |  dependency relationship: case
    word: that | head word: time |  dependency relationship: det
    word: time | head word: dates |  dependency relationship: obl
    word: dates | head word: root |  dependency relationship: root
    word: the | head word: intercourse |  dependency relationship: det
    word: direct | head word: intercourse |  dependency relationship: amod
    word: intercourse | head word: dates |  dependency relationship: obj
    word: with | head word: country |  dependency relationship: case
    word: this | head word: country |  dependency relationship: det
    word: country | head word: intercourse |  dependency relationship: nmod
    word: . | head word: dates |  dependency relationship: punct
    word: The | head word: list |  dependency relationship: det
    word: list | head word: contained |  dependency relationship: nsubj
    word: of | head word: tribute |  dependency relationship: case
    word: their | head word: tribute |  dependency relationship: nmod:poss
    word: tribute | head word: list |  dependency relationship: nmod
    word: contained | head word: root |  dependency relationship: root
    word: no | head word: jewels |  dependency relationship: det
    word: jewels | head word: contained |  dependency relationship: obj
    word: whatever | head word: throws |  dependency relationship: obj
    word: , | head word: throws |  dependency relationship: punct
    word: which | head word: fact |  dependency relationship: det
    word: fact | head word: throws |  dependency relationship: nsubj
    word: throws | head word: jewels |  dependency relationship: acl:relcl
    word: doubt | head word: throws |  dependency relationship: obj
    word: on | head word: tradition |  dependency relationship: case
    word: the | head word: tradition |  dependency relationship: det
    word: tradition | head word: throws |  dependency relationship: obl
    word: . | head word: contained |  dependency relationship: punct
    

# Discussion  

### On legnths: 
One of my expected initial takeaways between the original text and the English translation was their differences in lengths: the Chinese texts having 155 characters (including punctuations) whilst the translation had 233 words (incl. punctuations). This affirmed my rudimentary knowledge on Classical Chinese, also called "WenYan (direct word to word translation = "text speak"," for its signature brevity and concise use of characters compared to contemporary vernacular Chinese. In fact, if I was to do an Ugarit alignment between the vernacular version of the Hou Han Shu with its English translation, I would expect a much greater alignment percentage for the two modern languages share similar sentence structures/syntax. 

A big reason to the Classical Chinese's brevity is its lack of explicit verbs, different from vernacular English and Chinese. An example of this evidenced by Stanza's deparser is the third sentence in Classical Chinese, corresponding to the 4th sentence in English: 


"word: 其 | head word: 人 |  dependency relationship: det

word: 人 | head word: 質 |  dependency relationship: nsubj

word: 質 | head word: 。 |  dependency relationship: ccomp

word: 直 | head word: 質 |  dependency relationship: flat:vv

word: ， | head word: 質 |  dependency relationship: conj

word: 市 | head word: 无 |  dependency relationship: nmod

word: 无 | head word: ， |  dependency relationship: conj

word: 二 | head word: 价。谷 |  dependency relationship: nummod

word: 价。谷 | head word: 食 |  dependency relationship: nmod"

vs. 

"word: They | head word: honest |  dependency relationship: nsubj

word: are | head word: honest |  dependency relationship: cop

word: honest | head word: root |  dependency relationship: root

word: in | head word: transactions |  dependency relationship: case

word: their | head word: transactions |  dependency relationship: nmod:poss

word: transactions | head word: honest |  dependency relationship: obl

word: , | head word: are |  dependency relationship: punct

word: and | head word: are |  dependency relationship: cc

word: there | head word: are |  dependency relationship: expl

word: are | head word: honest |  dependency relationship: conj

word: no | head word: prices |  dependency relationship: det

word: double | head word: prices |  dependency relationship: amod

word: prices | head word: are |  dependency relationship: nsubj

word: . | head word: honest |  dependency relationship: punct"

From this exerpt alone, subtracting the punctuations, the Classical Chinese expressed the same idea as the English translation wuth just 8 characters, whilst the translation with added relations totaling 11 characters. In this comparison, the Chinese texts also contain no copula for identity predicate like the first "are" in English; rather, the Chinese directly follows the nominal subject "人 (people, in this case "they") with an adjective clause (flat multiword as well) "質直 (honest)." For the sake of brevity, Classical Chinese also relies on punctuations such as commas to replace the coordinating conjunction "and" used in the English translation. 

Another common trend in this section of the Hou Han Shu and Classical Chinese as a whole is their favored usage of the determiner "其" that appears several times in this passage alone. Not only does this determiner refer to the people of "Ta-ts'in (Daqin, or Rome) based on context, combined with "質直 (honest)," the Chinese text also eliminates the need for the oblique nominal of "transaction" which is an added phrase in the translation. 

### "Word to Word translation" vs. "Sense to Sense translation"
I had mentioned "word to word" translation earlier, which is the practice of translating similar to Ugarit alignment pairing a word with its direct parallel each time until one produces a finished translation. This school of translation is juxtaposed against "sense to sense translation," which prioritizes preserving the meaning of the text over the original grammar or syntax. Beginning with Roman authors/translators, the two schools already emerged as these Roman writers began the Western translation history. One of the leading forces in such field, St. Jerome, advocated for using different methods for different types of texts: a more "word to word" approach for literary translations and more "sense to sense" for religious translations (for religious translations were once the majority of Western translations).

The differences between these styles of translation manifest themselves in this project as well, due to the nature of technologies used such as Stanza treebanks and Ugarit. Beginning with Ugarit alignments, its word to word pairing function represents the "word to word" style, best fit for educating and learning about words on an individual level and their philologies. However, such nature of Ugarit posed a challenge in the project since Hirth's translation from late 19th century used a mixture of his imitation of Classical philologicla methods and more contemporary methods of rendering parallel meanings between texts. The difference methods of translation presented itself in the gap between Classical Chinese's 90% (140/155 words) alignment rate vs. the English translation's 58% (136/233 words) alignment rate.

Following the same exerpt above, the English translation of the two clauses actually highlights Hirth's use of both techniques in translating this text. Rather than going for a word by word practice, Hirth's insertion of the copula "are" and added contexts "in their transaction" shows his understanding of the meaning of the texts, thus rendering its original meaning in English. However, in the second clause, "市无二价," Hirth took on a far more literal and individual character based translation, perhaps due to the fact of the Chinese phrase's contemporary idiom status. Since the Hou Han Shu, the 4 character phrase has remained commonly used in literature and vernacular to describe honest transactions in the Chinese language. While the Classical Chinese Stanza understood the "二" as a numeric modifier (the charcter's literal definition is two), the English parser took Hirth's translation of "double" as an adjectival modifier. In the Ugarit alignment, you can also clearly see the more literal and word by word translation by Hirth, for every Chinese character corresponded to its English counterpart in order. In maintaing the word order and its rough word count, Hirth sacrificed a more intuitive translation, for he could've rendered "二" less literally as "double" based on its numeric definition, but rather its contextual definition of "other;" the following translation would be a more sense to sense approach of the idiom: "the market has no other price."

### On "Ta-ts'in (Rome)" and Proper Nouns
In the English translation, Hirth used homophonic translation to spell out the Han dynasty's official name for ancient Rome, "Ta-ts'in," or romanized as "Daqin." Since the Chinese text uses two separate characters to make up this name, 大秦, Stanza's deparser shows two different ways of the computer's interpretation of the original and English aliases. 

In the Classical Chinese parsing, Stanza attributes "大" as adjectival modifier and "秦" as nominal modifier for "大" has a literal translation of "big" in a stand alone scenario, while "秦" is more similar to a proper noun, since the Qin in Qin dynasty China shares the same character. So the Stanza deparsing of 大秦 is another example of word to word translation, when it should be instead translated based on historical context. In this contexts of "Daqin" or "Ta-ts'in" being a proper noun for the historical term of ancient Rome in Han China, the Stanza parsing for Classical Chinese gets delt the underhand in terms of human comprehension compared to its English deparsing for "Ta-ts" being a nominal modifier and "'in" being a flat multiword expression interpreting the whole phrase "Ta-ts'in" as a multiword proper noun. 

## Citations

Hirth, Friedrich, 1845-1927. China And the Roman Orient: Researches Into Their Ancient And Mediæval Relations As Represented In Old Chinese Records. Leipsic: G. Hirth, 1885.

Universal Dependency Relations, universaldependencies.org/u/dep/. 

“Dependency Parsing.” Stanza, stanfordnlp.github.io/stanza/depparse.html. 

“Book of Later Han 後漢書 卷八十八 西域傳 Volume 88: Treatise on the Western Regions.” Chinese Notes, chinesenotes.com/houhanshu.html. 

Guo, Y. and Wan, Y. (2022) Retracing the History of “Word for Word”, “Sense for Sense” Translation
—Confronting and Inheriting of the Ancient Roman Translation Theories. Open Journal of Modern Linguistics, 12, 568-577.

### Special thanks to Micah Saxton, Professor Gregory Crane, Peter Nadel, and Professor Man Xu for all the help on this project. 
