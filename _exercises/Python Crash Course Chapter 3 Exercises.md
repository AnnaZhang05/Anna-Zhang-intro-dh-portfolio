---
layout: page
title: Python Crash Course Chapter 3 Exercises
description: Codes from Chapter 3 Exercises in Python Crash Course. Week 4.
---

## 3-1



```python
names = ["Hannah", "Evren", "Sophia"]
print(names[0])
print(names[1])
print(names[2])
```

    Hannah
    Evren
    Sophia
    

## 3-2




```python
print(f"hey {names[0]}, what are you up to?")

```

    hey Hannah, what are you up to?
    


```python
print(f"hey {names[1]}, what are you up to?")
```

    hey Evren, what are you up to?
    


```python
print(f"hey {names[2]}, what are you up to?")
```

    hey Sophia, what are you up to?
    

## 3-3


```python
transportations = ["T", "subway", "bike"]
print(f"i like riding the {transportations[0]}")
print(f"i like riding the {transportations[1]}")
print(f"i like riding the {transportations[2]}")
```

    i like riding the T
    i like riding the subway
    i like riding the bike
    

## 3-4


```python
famous_people = ["mary shelley", "eliott smith", "kate bush"]
print(f"dear {famous_people[0].title()}, would you join me for dinner?")
print(f"dear {famous_people[1].title()}, would you join me for dinner?")
print(f"dear {famous_people[2].title()}, would you join me for dinner?")

```

    dear Mary Shelley, would you join me for dinner?
    dear Eliott Smith, would you join me for dinner?
    dear Kate Bush, would you join me for dinner?
    

## 3-5


```python
away = famous_people.pop(1)
print(f"{away.title()} sadly won't be joining us.")
```

    Eliott Smith sadly won't be joining us.
    


```python
another_person = "sufjan stevens"
famous_people.append(another_person)
print(f"dear {famous_people[0].title()}, would you join me for dinner?")
print(f"dear {famous_people[1].title()}, would you join me for dinner?")

```

    dear Mary Shelley, would you join me for dinner?
    dear Sufjan Stevens, would you join me for dinner?
    


```python
print("let's have a bigger party!")
```

    let's have a bigger party!
    


```python
famous_people.insert(0, "ira kaplan")
famous_people.insert(1, "fiona apple")
famous_people.append("vashti bunyan")
print(f"dear {famous_people[0].title()}, would you join me for dinner?")
print(f"dear {famous_people[1].title()}, would you join me for dinner?")
print(f"dear {famous_people[2].title()}, would you join me for dinner?")
print(f"dear {famous_people[3].title()}, would you join me for dinner?")
```

    dear Ira Kaplan, would you join me for dinner?
    dear Fiona Apple, would you join me for dinner?
    dear Mary Shelley, would you join me for dinner?
    dear Sufjan Stevens, would you join me for dinner?
    


```python
print("shoot, we can't fit all the new guests!")
```

    shoot, we can't fit all the new guests!
    


```python
famous_people.pop(2)
print(f"sorry {famous_people[2].title()} we can't dine together.")
      
```

    sorry Sufjan Stevens we can't dine together.
    


```python
famous_people.insert(0, "kate bush")
famous_people.insert(2, "fiona apple")
```


```python
famous_people.pop(1)
print(f"{famous_people[0].title()}, hope you can still join me.")
    
print(f"{famous_people[1].title()}, hope you can still join me.")



```

    Kate Bush, hope you can still join me.
    Fiona Apple, hope you can still join me.
    


```python
places = ["japan", "italy", "greece", "iceland"]
print(places)
```

    ['japan', 'italy', 'greece', 'iceland']
    


```python
print(sorted(places))
```

    ['greece', 'iceland', 'italy', 'japan']
    


```python
print(places)
```

    ['japan', 'italy', 'greece', 'iceland']
    


```python
places.reverse()
print(places)
```

    ['iceland', 'greece', 'italy', 'japan']
    


```python
places.reverse()
print(places)
```

    ['japan', 'italy', 'greece', 'iceland']
    


```python
places.sort()
print(places)

```

    ['greece', 'iceland', 'italy', 'japan']
    


```python
places.sort(reverse=True)
print(places)
```

    ['japan', 'italy', 'iceland', 'greece']
    


```python

```
