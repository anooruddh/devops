### What is Python?
Python is a widely-used, interpreted, object-oriented, and high-level programming language with dynamic semantics, used for general-purpose programming. It was created by Guido van Rossum, and first released on February 20, 1991.
  
The Zen of Python, by Tim Peters

    Beautiful is better than ugly.
    Explicit is better than implicit.
    Simple is better than complex.
    Complex is better than complicated.
    Flat is better than nested.
    Sparse is better than dense.
    Readability counts.
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    Errors should never pass silently.
    Unless explicitly silenced.
    In the face of ambiguity, refuse the temptation to guess.
    There should be one-- and preferably only one --obvious way to do it.
    Although that way may not be obvious at first unless you're Dutch.
    Now is better than never.
    Although never is often better than *right* now.
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    Namespaces are one honking great idea -- let's do more of those!  
      
### Is Python open source?

Yes, all modern versions of Python are copyrighted under a GPL-compatible license certified by the Open Source Initiative.  

### Create a Class

Let’s begin by creating a class:

    class Dog:                   

    def __init__(self,dogBreed,dogEyeColor):

        self.breed = dogBreed       
        self.eyeColor = dogEyeColor...

### How Does the __init__ Method Work? 
The __init__ method is the Python equivalent of the C++ constructor in an object-oriented approach. The __init__  function is called every time an object is created from a class. The __init__ method lets the class initialize the object’s attributes and serves no other purpose. It is only used within classes. 

he __init__ method uses the keyword self to assign the values passed as arguments to the object attributes **self.breed** and **self.eyeColor**.

### Example 1:
        class Dog:                

        def __init__(self, dogBreed,dogEyeColor): 

            self.breed = dogBreed   
            self.eyeColor = dogEyeColor

        tomita = Dog("Fox Terrier","brown")

        print("This dog is a",tomita.breed,"and his eyes are",tomita.eyeColor)
        
### Example 2:        
          class Dog:  
 
          def __init__(self):
              self.nbLegs = 4

          tomita = Dog()

          print("This dog has",tomita.nbLegs,"legs")
          
### Example 3:
        class Dog:                
 
        def __init__(self, dogBreed="German Shepherd",dogEyeColor="brown"): 

            self.breed = dogBreed   
            self.eyeColor = dogEyeColor

        tomita = Dog()

        print("This dog is a",tomita.breed,"and his eyes are",tomita.eyeColor)
        
### Difference between == and is operator in Python

The **Equality operator (==)** compares the values of both the operands and checks for value equality. Whereas the **'is'** operator checks whether both the operands refer to the same object or not (present in the same memory location)

      >>> a=[1,2,3]
      >>> b=[1,2,3]
      >>> c=[]
      >>> 
      >>> id(a)
      1836797086024
      >>> id(b)
      1836799675144
      >>> id(c)
      1836797085896
      >>> 
      >>> a == b
      True
      >>> a is b
      False
      >>> c=a
      >>> id(c)
      1836797086024
      >>> id(a)
      1836797086024
      >>> a == c
      True
      >>> a is c
      True
      >>> 
