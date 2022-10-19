---
layout: page
title: Python Crash Course Chapter 8 Exercises
description: Codes from Chapter 8 Exercises in Python Crash Course, week 7. 
---

## 8-1


```python
def display_message():
    print("i'm learning about functions in this chapter.")
```


```python
display_message()
```

    i'm learning about functions in this chapter.
    

## 8-2


```python
def favorite_book(title):
    print(f"one of my favorite books is {title.title()}.")
```


```python
favorite_book('the unbearable lightness of being')
```

    one of my favorite books is The Unbearable Lightness Of Being.
    

## 8-3


```python
def make_shirt(size, text):
    print(f"this is a {size} shirt with {text} on it.")
```


```python
make_shirt('medium', '"Loveless by my bloody valentine"')
```

    this is a medium shirt with "Loveless by my bloody valentine" on it.
    


```python
make_shirt(size = 'small', text = '"nice shirt"')
```

    this is a small shirt with "nice shirt" on it.
    

## 8-5


```python
def describe_city(city, country ='the U.S.'):
    print(f"{city} is in {country}.")
```


```python
describe_city('somerville')
```

    somerville is in the U.S..
    


```python
describe_city('medfield')
```

    medfield is in the U.S..
    


```python
describe_city(city = 'haikou', country = 'China')
```

    haikou is in China.
    

## 8-6


```python
def city_country(city, country):
    print(f'{city.title()}, {country.title()}')
```


```python
city_country('somerville', 'united states')
city_country('toronto', 'canada')
city_country('shanghai', 'china')
```

    Somerville, United States
    Toronto, Canada
    Shanghai, China
    

## 8-7


```python
def make_album(album, artist):
    fav_albums = {
        album.title(): artist
    }
    print(fav_albums)
```


```python
make_album(album = 'ants from up there', artist= 'Black Country, New Road')
```

    {'Ants From Up There': 'Black Country, New Road'}
    


```python
make_album('cardinal', 'Pinegrove')
```

    {'Cardinal': 'Pinegrove'}
    


```python
make_album('songs', 'Adrianne Lenker')
```

    {'Songs': 'Adrianne Lenker'}
    


```python

```
