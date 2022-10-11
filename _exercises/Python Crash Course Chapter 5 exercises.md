## 5-1


```python
food = 'ice cream'
print("is food == 'ice cream'? I predict True.")
print(food =='ice cream')

print("\nIs food == 'cookie'? I predict False.")
print(food == 'cookie')
```

    is food == 'ice cream'? I predict True.
    True
    
    Is food == 'cookie'? I predict False.
    False
    


```python
genre = 'novel'
print("is genre == 'novel'? I predict True.")
print(genre =='novel')

print("\nIs genre == 'nonfiction'? I predict False.")
print(genre == 'nonfiction')
```

    is genre == 'novel'? I predict True.
    True
    
    Is genre == 'nonfiction'? I predict False.
    False
    


```python
color = 'green'
print("is color == 'green'? I predict True.")
print(color =='green')

print("\nIs color == 'blue'? I predict False.")
print(color == 'blue')
```

    is color == 'green'? I predict True.
    True
    
    Is color == 'blue'? I predict False.
    False
    


```python
film = '35mm'
print("is film == '35mm'? I predict True.")
print(film =='35mm')

print("\nIs film == '120'? I predict False.")
print(film == '120')
```

    is film == '35mm'? I predict True.
    True
    
    Is film == '120'? I predict False.
    False
    


```python
app = 'twitter'
print("is app == 'twitter'? I predict True.")
print(app =='twitter')

print("\nIs app == 'facebook'? I predict False.")
print(app == 'facebook')
```

    is app == 'twitter'? I predict True.
    True
    
    Is app == 'facebook'? I predict False.
    False
    

## 5-2


```python
gift = 'postcard'
print("is gift == 'postcard'? I predict True.")
print(gift =='postcard')
```

    is gift == 'postcard'? I predict True.
    True
    


```python
camera = 'SLR'
print("is camera.lower() == 'slr'? I predict True.")
print(camera.lower() =='slr')
```

    is camera.lower() == 'slr'? I predict True.
    True
    


```python
print('5+3>7? I predict True.')
print(5+3>7)
print('12 == 3*5? I predict False')
print(12 == 3*5)
```

    5+3>7? I predict True.
    True
    12 == 3*5? I predict False
    False
    


```python
print('is 5+3>7 and 12 == 3*5? I predict False')
print(5+3>7 and 12 == 3*5)
print('is 5+3>7 or 12 == 3*5? I predict True')
print(5+3>7 or 12 == 3*5)
```

    is 5+3>7 and 12 == 3*5? I predict False
    False
    is 5+3>7 or 12 == 3*5? I predict True
    True
    


```python
jewlries = ['necklace', 'rings', 'earrings']
print('is glasses in jewlries? I predict False')
print('glasses' in jewlries)
```

    is glasses in jewlries? I predict False
    False
    


```python
jewlries = ['necklace', 'rings', 'earrings']
print('is glasses not in jewlries? I predict True')
print('glasses' not in jewlries)
```

    is glasses not in jewlries? I predict True
    True
    

## 5-6


```python
age = 22
if age <2:
    print('you are a baby!')
elif 2 <= age < 4:
    print('you are a toddler.')
elif 4 <= age < 13:
    print('you are a kid.')
elif 13 <= age < 20:
    print('you are a teenager!')
elif 20 <= age < 65:
    print('you are an adult...')
else:
    print('you are an elder.')
```

    you are an adult...
    

## 5-7


```python
favorite_fruits = ['mango', 'strawberry', 'coconut']
if 'bananas' not in favorite_fruits:
    print('you really do not like bananas')
if 'mango' in favorite_fruits:
    print('you really like mango!')
if 'strawberry' in favorite_fruits:
    print('you really like strawberry!')
if 'apple' in favorite_fruits:
    print('you like apples?')
if 'coconut' in favorite_fruits:
    print('you really like coconut!')
```

    you really do not like bananas
    you really like mango!
    you really like strawberry!
    you really like coconut!
    

## 5-8


```python
username = ['anna', 'katie', 'admin', 'bill', 'hanna']
for u in username:
    if u == 'admin':
        print(f'Hello {u}, would you like to see a status report?')
    else:
        print(f'Hello {u}, thank you for logging in again.')
```

    Hello anna, thank you for logging in again.
    Hello katie, thank you for logging in again.
    Hello admin, would you like to see a status report?
    Hello bill, thank you for logging in again.
    Hello hanna, thank you for logging in again.
    

## 5-9


```python
username = ['anna', 'katie', 'admin', 'bill', 'hanna']
username.clear()
for u in username:
    if u == 'admin':
        print(f'Hello {u}, would you like to see a status report?')
else: print('We need to find some users!')
```

    We need to find some users!
    

## 5-10


```python
current_users = ['anna', 'katie', 'admin', 'bill', 'hanna']
new_users = ['charlie3', 'anna', 'wxaXD', 'hanna', 'user20']
for n in new_users:
    if n in current_users:
        print(f"{n} is taken, please make a new one!")
    else: 
        print(f"{n} is avaliable!")
```

    charlie3 is avaliable!
    anna is taken, please make a new one!
    wxaXD is avaliable!
    hanna is taken, please make a new one!
    user20 is avaliable!
    


```python

```
