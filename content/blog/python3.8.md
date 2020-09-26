+++
categories = ["python"]
comments = false
date = "2019-11-19T16:59:13-00:00"
draft = false
showpagemeta = true
slug = ""
tags = ["python", "python3.8", "walrus", "coding", "software"]
title = "New Python 3.8 feature selection  "
description = "This article will put some light on three the most useful Python 3.8 features"

+++
 
This article will put some light on three the most useful, in my opinion,     
features that come in a latest Python release. The purpose of such selection is to quickly introduce you with new and cool stuff that came with 3.8 version and allow you to use it just right now.     
    
1. ## Consistent logic with a brand new operator - **walrus**    
 ![walrus](https://images.unsplash.com/photo-1460899960812-f6ee1ecaf117?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1600&q=80)  
 > source: https://unsplash.com/  

Why walrus? Probably, because of visual representation which may be interpreted as two eyes and long parallel teeth - **:=**.    
It's very useful operator that allows to write more consistent code and it's something that I personally missed in any of programming languages. Let's dive into some examples:    
    
Let's have some data to process:    
    
``` 
data = {    
  'users': [ 'peter', 'john' ]
} ```    
```

We'd like to check if there are some `users` in our data and then print them amount. We can check if data contains 'users' key and then again retrieve values from data to count retrieved list length.    

### Before Python 3.8   

 ``` 
if 'users' in data:    
  print(f'There are {len(data['users')} users!')
>> There are 2 users!
```   

We can also store it in a variable to optimize dict search operations:    
  
``` 
users = data.get('users', []) if users:    
print(f'There are {len(users)} users!') 
>> There are 2 users!
```
  
 ### In Python 3.8 - with the *walrus* operator   
  
 ``` 
if users := data.get('users', []):    
  print(f'There are {len(users)} users!')>> There are 2 users! 
```
      
Much cleaner and simpler, right? It's also useful when we have to some more complex calculation in any of some larger expressions.    
    
### Walrus in list comprehensions    

 Generate some slugs (if possible) from some database objects in Django: 
 
``` 
from django.utils.text import slugify 
from app.models import Product    

[slug for product in Product.objects.all() if slug := slugify(product.name)] 
``` 

*slugify* may result with empty string if source string contains unsupported characters only (other than alphanumerics, underscore and hyphens). With such comprehension we'll get only possible slugs.    
    
Yeah! Now we can hack a bit more with python comprehensions.    
    
2. ## Fast logging with the new _f-string_ capabilities.    
 In every system we need to store some logs during application lifetime. It's nice to use Python 3 string formatting,    
cause it allows to mix string message with variable values and function results.    
    
Let's log some _counter_ value with f-string:    
    
``` 
print(f'Current counter equals {counter}') 
>> Current counter equals 11 
```    

 Still cool and consistent but let's use new Python:    
    
``` 
print(f'Current {counter=}') 
>> Current counter=11 
```    

Extremely useful when we need to track variable values with their names. Every name refactoring will also automatically update our log messages!    
    
We could also add some format specifiers that would convert our objects to another type:  
  
```  
class Cat:  
  legs = 4  
  def __str__(self): 
    return f'I am a cat with {self.legs=}'  
  
object = Cat()  
print(f'Who are you? I am an {object=!s}')  
>> Who are you? I am an object=I am a cat with self.legs=4  
```  

  3. ## Reversed dict iteration    
 
As we know, dicts became ordered by insertion in Python 3.6 for CPython implementation. We can use,  standard *dict* just like *OrderedDict*. Now, with Python 3.8, we can also iterate over dicts in reversed  order. Let's take a look on the example:  
  
```  
  
states = {  
 'idle': { 'id': 1 }, 
 'processing': { 'id': 2 }, 
 'post-processing': { 'id': 3 }, 
 'finished': { 'id': 4 }
}  

print('States log:')  
for state in reversed(states):  
 print(f'{state} id: {states[state]["id"]}')  

>> States log:  
>> finished id: 4  
>> post-processing id: 3  
>> processing id: 2  
>> idle id: 1 
```  
  
## Conclusion  
  
The three features that I selected from the new version of Python 3.8 bring most practical benefits in my opinion. I believe that with such introduction you will be able to start using them right away. Warlus operator can simplify your complex logic.  String formatting improvements also should allow you to write more consistent code. Reverse iteration and other CPython improvements and optimizations can be also very helpful.  

Go ahead and give a try to Python 3.8!