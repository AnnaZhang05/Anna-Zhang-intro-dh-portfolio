---
layout: page
title: Python Crash Course Chapter 6 Exercises
description: Codes from Chapter 6 Exercises in Python Crash Course, week 6. 
---

## 6-1


```python
person = {'first_name': 'hanna', 
          'last_name': 'jiang',
          'age': 19,
          'city': 'amherst'
}
person
```




    {'first_name': 'hanna', 'last_name': 'jiang', 'age': 19, 'city': 'amherst'}



## 6-2


```python
fav_numbers = {
    'anna': 17,
    'charlie': 4,
    'hanna': 12,
    'evren': 18,
    'katie': 10
}
print(fav_numbers['anna'])
print(fav_numbers['charlie'])
print(fav_numbers['hanna'])
print(fav_numbers['evren'])
print(fav_numbers['katie'])
```

    17
    4
    12
    18
    10
    

## 6-3


```python
glossary = {
    'list': 'a variable containing multiple items',
    'string': 'a series of characters',
    'variables': 'variables store data based on given values',
    'dictionary': 'store data in key value pairs',
    'booleans': 'data type used to define true or false questions'
}
for key, value in glossary.items():
    print(f"Key: {key}")
    print(f"Value: {value}")
```

    Key: list
    Value: a variable containing multiple items
    Key: string
    Value: a series of characters
    Key: variables
    Value: variables store data based on given values
    Key: dictionary
    Value: store data in key value pairs
    Key: booleans
    Value: data type used to define true or false questions
    

## 6-4


```python
glossary = {
    'list': 'a variable containing multiple items',
    'string': 'a series of characters',
    'variables': 'variables store data based on given values',
    'dictionary': 'store data in key value pairs',
    'booleans': 'data type used to define true or false questions',
    'f-string': 'strings beginning with f" and begins with a variable',
    'method': 'function belonging to an object',
    'for loops': 'for loops process over every item in a sequence',
    'if statement': 'code that will only execute if the condition is met',
    'else statement': 'code that executes the conditional statement'
}
for key, value in glossary.items():
    print(f"Key: {key}")
    print(f"Value: {value}")
```

    Key: list
    Value: a variable containing multiple items
    Key: string
    Value: a series of characters
    Key: variables
    Value: variables store data based on given values
    Key: dictionary
    Value: store data in key value pairs
    Key: booleans
    Value: data type used to define true or false questions
    Key: f-string
    Value: strings beginning with f" and begins with a variable
    Key: method
    Value: function belonging to an object
    Key: for loops
    Value: for loops process over every item in a sequence
    Key: if statement
    Value: code that will only execute if the condition is met
    Key: else statement
    Value: code that executes the conditional statement
    

## 6-5


```python
rivers = {
    'nile': 'egypt',
    'yangtze': 'china',
    'po': 'italy'   
}
for r in rivers.keys():
    print(r)
for c in rivers.values(): 
    print(c)
for key, value in rivers.items():
    print(f"the {key} river runs through {value}.")
```

    nile
    yangtze
    po
    egypt
    china
    italy
    the nile river runs through egypt.
    the yangtze river runs through china.
    the po river runs through italy.
    

## 6-7


```python
person_1 = {'first_name': 'hanna', 
          'last_name': 'jiang',
          'age': 19,
          'city': 'amherst'
}
person_2 = {'first_name': 'charlie',
           'last_name': 'ewald',
            'age': 19,
            'city': 'medford'
           }
person_3 = {
    'first_name': 'molly',
           'last_name': 'goonan',
            'age': 19,
            'city': 'rochester'
}
people = [person_1, person_2, person_3]
print(people)
```

    [{'first_name': 'hanna', 'last_name': 'jiang', 'age': 19, 'city': 'amherst'}, {'first_name': 'charlie', 'last_name': 'ewald', 'age': 19, 'city': 'medford'}, {'first_name': 'molly', 'last_name': 'goonan', 'age': 19, 'city': 'rochester'}]
    

## 6-8


```python
p1 = {
    'type': 'cat',
    'owner': 'angel'
}
p2 = {
    'type': 'dog',
    'owner': 'theo'
}
p3 = {
    'type': 'dog',
    'owner': 'molly'
}
pets = [p1, p2, p3]
print(pets)
```

    [{'type': 'cat', 'owner': 'angel'}, {'type': 'dog', 'owner': 'theo'}, {'type': 'dog', 'owner': 'molly'}]
    

## 6-9


```python
favorite_places ={
    'anna': 'haikou',
    'charlie': 'cotuit bridge',
    'molly': 'uroch'
}
for key, value in favorite_places.items():
    print(key, value)
```

    anna haikou
    charlie cotuit bridge
    molly uroch
    

## 6-11


```python
cities = {
    'somerville': {
        'country': "united states",
        'population': 81175,
        'fact': 'nick name used to be slummerville'
    },
    'portland': {
        'country': 'united states',
        'population': 66706,
        'fact': 'state theatre is in portland.'
    },
    'bratislava': {
        'country': 'slovakia',
        'population': 424428,
        'fact': 'it is the only capital that borders two other countries.'
    }
}
print(cities.items())
```

    dict_items([('somerville', {'country': 'united states', 'population': 81175, 'fact': 'nick name used to be slummerville'}), ('portland', {'country': 'united states', 'population': 66706, 'fact': 'state theatre is in portland.'}), ('bratislava', {'country': 'slovakia', 'population': 424428, 'fact': 'it is the only capital that borders two other countries.'})])
    


```python

```
