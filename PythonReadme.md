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
    
### Compiled or Interpreted

Python is an interpreted language, which means the source code of a Python program is converted into bytecode that is then executed by the Python virtual machine. Python is different from major compiled languages, such as C and C + +, as Python code is not required to be built and linked like code for these languages.

### PIP

PIP is a package-management system written in Python used to install and manage software packages. It connects to an online repository of public packages, called the Python Package Index

### Python Virtual Environment      

A virtual environment is a tool that helps to keep dependencies required by different projects separate by creating isolated python virtual environments for them. This is one of the most important tools that most of the Python developers use.

### Why do we need a virtual environment?

Imagine a scenario where you are working on two web based python projects and one of them uses a Django 1.9 and the other uses Django 1.10 and so on. In such situations virtual environment can be really useful to maintain dependencies of both the projects.

Installing virtualenv

          pip install virtualenv
          
          virtualenv --version
          
Creating a virtual environment

          virtualenv my_name
          python -m venv frontend

Activating a virtual environment

        source /opt/virenvs/frontend/bin/activate

Deactivating a virtual environment

        deactivate  ((virtualenv_name)$ deactivate)
        

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

### Python Indentation
Indentation refers to the spaces at the beginning of a code line.

Where in other programming languages the indentation in code is for readability only, the indentation in Python is very important.

Python uses indentation to indicate a block of code.

Example

Correct syntax

        if 5 > 2:
          print("Five is greater than two!")

        if a == b:
            print('a and b are EQUAL')
        else:
            print('NOT EQUAL')
    
In-Correct syntax

        if 5 > 2:
        print("Five is greater than two!")
        
        
### Creating a Comment

Comments starts with a #, and Python will ignore them:

Single Line Comment (#)

        #This is a comment
        print("Hello, World!")
        
Multi-Line Comment ( 3 " or 3 '  - starting and ending)

        """
        This is a comment
        written in
        more than just one line
        """
        print("Hello, World!")

### Many Values to Multiple Variables


Python allows you to assign values to multiple variables in one line:

        x, y, z = "Orange", "Banana", "Cherry"
        
Note: Make sure the number of variables matches the number of values, or else you will get an error.

And you can assign the same value to multiple variables in one line:

        x = y = z = "Orange"

        >>> fruits = ["apple", "banana", "cherry"]
        >>> x,y,z = fruits
        >>> print(z)
        cherry
        >>> print(y)
        banana
        >>> print(x)
        apple

### Python frozenset() Function

Its a built-in python function, Which Freeze the list, and make it unchangeable / immutable:

Example

          mylist = ['apple', 'banana', 'cherry']
          x = frozenset(mylist)
        
Note - if you try to change list element in frozen set, it will throw error **TypeError: 'frozenset' object does not support item assignment**        

          list1= [1,2,3]
          list2= ['apple', 'banana', 'cherry']
          x = frozenset(list2)

          print(list1)  # [1, 2, 3]
          print(list2)  # ['apple', 'banana', 'cherry']
          print(x)	  # frozenset({'banana', 'apple', 'cherry'})


          list1[0]=4
          print(list1)  # [4, 2, 3]
          x[0]='mango'  # TypeError: 'frozenset' object does not support item assignment. 
          print(x)  


### Python break, Pass and continue

In Python, break and continue statements can alter the flow of a normal loop.

Loops iterate over a block of code until the test expression is false, but sometimes we wish to terminate the current iteration or even the whole loop without checking test expression.

The break and continue statements are used in these cases.

### break
The break statement terminates the loop containing it. Control of the program flows to the statement immediately after the body of the loop.

If the break statement is inside a nested loop (loop inside another loop), the break statement will terminate the innermost loop.

# Program to show the use of continue statement inside loops

          for val in "string":
              if val == "i":
                  break
              print(val)

          print("The end")

Outpul

          s
          t
          r
          The end
          > 

In this program, we iterate through the "string" sequence. We check if the letter is i, upon which we break from the loop. Hence, we see in our output that all the letters up till i gets printed. After that, the loop terminates.
-------------------------------------------------------------------------------------------------------------------

### continue 

The continue statement is used to skip the rest of the code inside a loop for the current iteration only. Loop does not terminate but continues on with the next iteration.

# Program to show the use of continue statement inside loops

          for val in "string":
              if val == "i":
                  continue
              print(val)

          print("The end")

Output

          s
          t
          r
          n
          g
          The end
          
We continue with the loop, if the string is i, not executing the rest of the block. Hence, we see in our output that all the letters except i gets printed.

### pass

In Python programming, the pass statement is a null statement. The difference between a comment and a pass statement in Python is that while the interpreter ignores a comment entirely, pass is not ignored.

However, nothing happens when the pass is executed. It results in no operation (NOP).

We generally use it as a placeholder

Suppose we have a loop or a function that is not implemented yet, but we want to implement it in the future. They cannot have an empty body. The interpreter would give an error. So, we use the pass statement to construct a body that does nothing.

              '''pass is just a placeholder for
              functionality to be added later.'''
              sequence = {'p', 'a', 's', 's'}
              for val in sequence:
                  pass

:white_check_mark: The **break** statement in Python terminates the current loop and resumes execution at the next statement, just like the traditional break found in C.

:white_check_mark:  The **continue** statement in Python returns the control to the beginning of the while loop. The continue statement rejects all the remaining statements in the current iteration of the loop and moves the control back to the top of the loop.

:white_check_mark: The **pass** statement in Python is used when a statement is required syntactically but you do not want any command or code to execute.

### Python Array

Arrays in python can only contain elements of same data types

          cars = ["Ford", "Volvo", "BMW"]
          num  = [ 1,2,3]

### Python List
List is an ordered sequence of items. It is one of the most used datatype in Python and is very flexible. All the items in a list do not need to be of the same type.

Declaring a list is pretty straight forward. Items separated by commas are enclosed within brackets [ ].

        a = [1, 2.2, 'python']
        x = [5,5,5,5,1,2,3,4,4]  
        print(x)
        output
        [5, 5, 5, 5, 1, 2, 3, 4, 4] 
      
### Python Tuple
Tuple is an ordered sequence of items same as a list. The only difference is that tuples are immutable. Tuples once created cannot be modified.

Tuples are used to write-protect data and are usually faster than lists as they cannot change dynamically.

It is defined within parentheses () where items are separated by commas.

        t = (5,'program', 1+3j)
        
### Python Set
Set is an unordered collection of unique items. Set is defined by values separated by comma inside braces { }. Items in a set are not ordered.

         y = {5,1,2,3,4,4}
         print(y)
         
         output
         {1, 2, 3, 4, 5} 

### Python Dictionary
Dictionary is an unordered collection of key-value pairs.

It is generally used when we have a huge amount of data. Dictionaries are optimized for retrieving data. We must know the key to retrieve the value.

In Python, dictionaries are defined within braces {} with each item being a pair in the form key:value. Key and value can be of any type.

          d = {'Name':'anooruddh','age':'40'}
          print(d)
          
          output
          {'Name': 'anooruddh', 'age': '40'}
          
### Quick Note about all four

## Lists

            List is a collection which is ordered.
            Lists are mutable (changeable) .
            Allows duplicate members
            Brackets used to represent: []
            Lists are like arrays declared in other languages.


## Tuples
            Collection of items which is ordered.
            Tuples are immutable (unchangeable) .
            Brackets used to represent: ()
            Only difference between tuples and lists are that lists can be changed.
            Tuples are faster than lists as they are immutable.


## Sets
            Collection of Unordered and Unindexed items.
            Sets are mutable (changeable).
            Does not take duplicate Values.
            Sets are unordered, so you cannot be sure in which order the items will appear.
            Brackets used to represent: { }.
            Sets are not faster than lists however they have a upper hand when it comes to membership testing.


## Dictionaries

            Key:Value Pair in Python
            A dictionary is a collection which is unordered, changeable and indexed.
            In Python dictionaries are written with curly brackets, and they have keys and values.
            Brackets used to represent: {}.


### Namespaces ( way of naming and organizing a python object )

A namespace is a system that has a unique name for each and every object in Python. An object might be a variable or a method. Python itself maintains a namespace in the form of a Python dictionary

In a Python program, there are four types of namespaces: (This is the LEGB rule)

:white_check_mark:  Built-In  - If it can’t find x anywhere else, then the interpreter tries the built-in scope.

:white_check_mark:  Global - If neither of the above searches is fruitful, then the interpreter looks in the global scope next.

:white_check_mark:  Enclosing -  If x isn’t in the local scope but appears in a function that resides inside another function, then the interpreter searches in the enclosing function’s scope.

:white_check_mark:  Local - If you refer to x inside a function, then the interpreter first searches for it in the innermost scope that’s local to that function.
    
These have differing lifetimes. As Python executes a program, it creates namespaces as necessary and deletes them when they’re no longer needed. Typically, many namespaces will exist at any given time.
