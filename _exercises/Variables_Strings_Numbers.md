---
layout: page
title: Variables, Strings, and Numbers
description: Notebook with code about variables, strings, and numbers. From week 3-4, Fall '22.
---

# Variables
```python
greeting = "Hello world!"
```


```python
print(greeting)
```

    Hello world!
    


```python
ToDo = "AAC meeting"
print(ToDo)
```

    AAC meeting
    


```python
ToDo = "my work"
print(ToDo)
```

    my work
    


```python
ex_1 = "this is a string."
print(ex_1)
```

    this is a string.
    


```python
ex_2 = 'this is also a string.'
print(ex_2)
```

    this is also a string.
    


```python
ex_3 = "here is a string'
print(ex_3)
```


      Input In [11]
        ex_3 = "here is a string'
                                 ^
    SyntaxError: EOL while scanning string literal
    



```python
ex_4 = 'i asked the question, "when should i use single quotes?"'
print(ex_4)
```

    i asked the question, "when should i use single quotes?"
    


```python
historian = "edward gibbon"
print(historian)
```

    edward gibbon
    


```python
print(historian.title())
```

    Edward Gibbon
    


```python
novelist = "becky chambers"
print(novelist.lower())
```

    becky chambers
    


```python
"becky" == "becky"
```




    True




```python
"becky" == "Becky"
```




    False




```python
first_name = "ada"
last_name = "lovelace"
```


```python
full_name = first_name + " " + last_name
print(full_name)
```

    ada lovelace
    


```python
greeting = f" Hello, {first_name} {last_name}"
print(greeting.title())
```

     Hello, Ada Lovelace
    

 ## whitespace






```python
print("\ttab")
```

    	tab
    


```python
print("Languages: \nGreek\nLatin")
```

    Languages: 
    Greek
    Latin
    


```python
str_rspace = "string       "
print(str_rspace)
print(str_rspace.rstrip())
```

    string       
    string
    


```python
example = str_rspace + "."
print(example)
```

    string       .
    


```python
example_2 = str_rspace.rstrip() + "."
print(example_2)
```

    string.
    

## numbers



```python
#  integers
32+9

```




    41




```python
num =5
num +5 


```




    10




```python
type(num
    )
```




    int




```python
30/10 # this will run a float, not int


```




    3.0




```python
f = 30 / 8
```


```python
f
```




    3.75




```python
3**2

```




    9




```python

```
