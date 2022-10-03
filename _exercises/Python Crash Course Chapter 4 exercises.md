---
layout: page
title: Python Crash Course Chapter 4 Exercises
description: Codes from Chapter 4 Exercises in Python Crash Course, week 4. 
---

## 4-1


```python
pizza = ["cheese", "margherita", "mushroom"]
for p in pizza:
    print(f"{p} pizza")
    print(f" i like {p} pizza")
    
print("i like pizza A LOT")
```

    cheese pizza
     i like cheese pizza
    margherita pizza
     i like margherita pizza
    mushroom pizza
     i like mushroom pizza
    i like pizza A LOT
    

## 4-2


```python
animals = ["bunnies", "cats", "birds"]
for animal in animals: 
    print(f"{animal} are cute")
print("they are all cute")
```

    bunnies are cute
    cats are cute
    birds are cute
    they are all cute
    


```python
for num in range(1,21):
    print(num)
```

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    

## 4-6



```python
for num in range (1, 21, 2):
    print(num)
```

    1
    3
    5
    7
    9
    11
    13
    15
    17
    19
    

## 4-10


```python
animals = ["bunnies", "cats", "birds", "dogs", "turtles", "monkeys"]
print("the first three animals are:")
for a in animals[0:3]:
    print("\t" + a)
    
print("the middle three animals are:")
for a in animals[2:5]:
    print("\t" + a)
    
print("the last three animals are:")
for a in animals[3:6]:
    print("\t" + a)
```

    the first three animals are:
    	bunnies
    	cats
    	birds
    the middle three animals are:
    	birds
    	dogs
    	turtles
    the last three animals are:
    	dogs
    	turtles
    	monkeys
    

## 4-11



```python
friend_pizzas = ["cheese", "margherita", "mushroom"]
pizza.append("pepperoni")
friend_pizzas.append("onion")
print("my favorite pizzas are:")
for p in pizza:
    print("\t" + p)

print("my friend's favorite pizzas are:")
for p2 in friend_pizzas:
    print("\t" + p2)
```

    my favorite pizzas are:
    	cheese
    	margherita
    	mushroom
    	pepperoni
    	pepperoni
    my friend's favorite pizzas are:
    	cheese
    	margherita
    	mushroom
    	onion
    


```python

```
