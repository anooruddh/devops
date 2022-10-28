### What is Python?
Python is a widely-used, interpreted, object-oriented, and high-level programming language with dynamic semantics, used for general-purpose programming. It was created by Guido van Rossum, and first released on February 20, 1991.

# Python is an interpreted language

# The Virtual Machine is the runtime engine of Python and it is always present as part of the Python system, and is the component that truly runs the Python scripts

# PIP installation

## Supported Methods
If your Python environment does not have pip installed, there are 2 mechanisms to install pip supported directly by pip’s maintainers:

### ensurepip
### get-pip.py

### ensurepip

Python comes with an ensurepip module1, which can install pip in a Python environment.
            
            python -m ensurepip --upgrade

### get-pip.py

This is a Python script that uses some bootstrapping logic to install pip.

Download the script, from https://bootstrap.pypa.io/get-pip.py.

Open a terminal/command prompt, cd to the folder containing the get-pip.py file and run:

            python get-pip.py

## Upgrading pip

            python -m pip install --upgrade pip



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

Program to show the use of continue statement inside loops

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


### Decorators in Python

A decorator is a design pattern in Python that allows a user to add new functionality to an existing object without modifying its structure. Decorators are usually called before the definition of a function you want to decorate

Creating Decorators

We do this by defining a wrapper inside an enclosed function. As you can see it very similar to the function inside another function that we created earlier.

            def uppercase_decorator(function):
                def wrapper():
                    func = function()
                    make_uppercase = func.upper()
                    return make_uppercase

                return wrapper
                
Result without decorator

              def say_hi():
                return 'hello there'
              
              say_hi()
              OUTPUT -  hello there           

Result with decorator

              @uppercase_decorator
              def say_hi():
                  return 'hello there'
                  
              say_hi()
              OUTPUT - HELLO THERE

# DJango

### What is Django?

Django is a free, open source, Python-based web framework that follows the Model-View-Template (MVT) architectural pattern. It reduces the hassle of web development so that you can focus on writing your app instead of reinventing the wheel.

### What is a REST API?

A REST API is a popular way for systems to expose useful functions and data. REST, which stands for representational state transfer, can be made up of one or more resources that can be accessed at a given URL and returned in various formats, like JSON, images, HTML, and more.

### Why Django REST framework?

Django REST framework (DRF) is a powerful and flexible toolkit for building Web APIs. Its main benefit is that it makes serialization much easier.

Django REST framework is based on Django’s class-based views, so it’s an excellent option if you’re familiar with Django. It adopts implementations like class-based views, forms, model validator, QuerySet, and more.

### Setting up Django REST framework

Ideally, you’d want to create a virtual environment to isolate dependencies, however, this is optional. Run the command python -m venv django_env from inside your projects folder to create the virtual environment. Then, run source ./django_env/bin/activate to turn it on.

Keep in mind that you’ll need to reactivate your virtual environment in every new terminal session. You’ll know that it is turned on because the environment’s name will become part of the shell prompt.

Navigate to an empty folder in your terminal and install Django and Django REST framework in your project with the commands below:

            pip install django
            pip install django_rest_framework
            
Create a Django project called todo with the following command:

            django-admin startproject todo
            
Then, cd into the new todo folder and create a new app for your API:

            django-admin startapp todo_api
            
Run your initial migrations of the built-in user model:

            python manage.py migrate

Next, add rest_framework and todo to the INSTALLED_APPS inside the todo/todo/settings.py file:

            # settings.py
            INSTALLED_APPS = [
                'django.contrib.admin',
                'django.contrib.auth',
                'django.contrib.contenttypes',
                'django.contrib.sessions',
                'django.contrib.messages',
                'django.contrib.staticfiles',
                'rest_framework',
                'todo_api'
            ]
Create a serializers.py and urls.py file in todo/todo_api and add new files as configured in the directory structure below:

                ├── todo
                │   ├── __init__.py
                │   ├── settings.py
                │   ├── urls.py
                ├── db.sqlite3
                ├── manage.py
                └── todo_api
                    ├── admin.py
                    ├── serializers.py
                    ├── __init__.py
                    ├── models.py
                    ├── urls.py
                    └── views.py
                    
Be sure to include rest_framework and URLs as shown below in your main urls.py file:

              # todo/todo/urls.py : Main urls.py
              from django.contrib import admin
              from django.urls import path, include
              from todo_api import urls as todo_urls

              urlpatterns = [
                  path('admin/', admin.site.urls),
                  path('api-auth/', include('rest_framework.urls')),
                  path('todos/', include(todo_urls)),
              ]
              
Next, create a superuser. We’ll come back to this later:

              python manage.py createsuperuser
              
RESTful structure: GET, POST, PUT, and DELETE methods

In a RESTful API, endpoints define the structure and usage with the GET, POST, PUT, and DELETE HTTP methods. You must organize these methods logically.

# Difference between .py and .pyc files?

Python compiles the .py files and saves it as .pyc files , so it can reference them in subsequent invocations. The .pyc contain the compiled bytecode of Python source files. The .pyc contain the compiled bytecode of Python source files, which is what the Python interpreter compiles the source to. This code is then executed by Python's virtual machine . There's no harm in deleting them (.pyc), but they will save compilation time if you're doing lots of processing.

For example, When you run myprog.py source file, the python interpreter first looks to see if any 'myprog.pyc' (which is the byte-code compiled version of 'myprog.py') exists, and if it is more recent than 'myprog.py'. If so, the interpreter runs it. If it does not exist, or 'myprog.py' is more recent than it (meaning you have changed the source file), the interpreter first compiles 'myprog.py' to 'myprog.pyc'.


# How to Protect Python Source Code

## Compile using CPython
## Wrap with Cython ( Cython is a superset of Python )
## Licence your python code 

# What is Cython?

A superset of Python that compiles to C, Cython combines the ease of Python and the speed of native code
Cython source file names consist of the name of the module followed by a .pyx extension, for example a module called primes would have a source file named primes.pyx.

Compilation Stage

 - A .pyx file is compiled by Cython to a .c file.
 - The .c file is compiled by a C compiler to a .so file (or a .pyd file on Windows)
 
Compiling from the command line

 - The cython command takes a .py or .pyx file and compiles it into a C/C++ file.
 - The cythonize command takes a .py or .pyx file and compiles it into a C/C++ file. It then compiles the C/C++ file into an extension module which is directly importable from Python.
 
Compiling with the cython command
 
            cython primes.pyx

cythonize command

            cythonize -a -i yourmod.pyx

# *args and **kwargs in Python

Special Symbols Used for passing arguments:-

***args (Non-Keyword Arguments)
**kwargs (Keyword Arguments)

## What is Python *args ?

The special syntax *args in function definitions in python is used to pass a variable number of arguments to a function. It is used to pass a non-key worded, variable-length argument list. 

The syntax is to use the symbol * to take in a variable number of arguments; by convention, it is often used with the word args.

What *args allows you to do is take in more arguments than the number of formal arguments that you previously defined. With *args, any number of extra arguments can be tacked on to your current formal parameters (including zero extra arguments).

For example, we want to make a multiply function that takes any number of arguments and is able to multiply them all together. It can be done using *args.

Using the *, the variable that we associate with the * becomes an iterable meaning you can do things like iterate over it, run some higher-order functions such as map and filter, etc.

### Python program to illustrate *args for a variable number of arguments

## Example 1: 
                        def myFun(*argv):
                                    for arg in argv:
                                                print(arg)


                        myFun('Hello', 'Welcome', 'to', 'GeeksforGeeks')

                        Output:

                        Hello
                        Welcome
                        to
                        GeeksforGeeks
                        
### Python program to illustrate *args with a first extra argument                        

## Example 2: 
                        def myFun(arg1, *argv):
                                    print("First argument :", arg1)
                                    for arg in argv:
                                                print("Next argument through *argv :", arg)


                        myFun('Hello', 'Welcome', 'to', 'GeeksforGeeks')

                        Output:

                        First argument : Hello
                        Next argument through *argv : Welcome
                        Next argument through *argv : to
                        Next argument through *argv : GeeksforGeeks
                        
## What is Python **kwargs

The special syntax **kwargs in function definitions in python is used to pass a keyworded, variable-length argument list. We use the name kwargs with the double star. The reason is that the double star allows us to pass through keyword arguments (and any number of them).

A keyword argument is where you provide a name to the variable as you pass it into the function.

One can think of the kwargs as being a dictionary that maps each keyword to the value that we pass alongside it. That is why when we iterate over the kwargs there doesn’t seem to be any order in which they were printed out.               
         
## Example 1: 

Python program to illustrate *kwargs for a variable number of keyword arguments. Here **kwargs accept keyworded variable-length argument passed by function call. for first=’Geeks’ first is key and ‘Geeks’ is a value. in simple words, what we assigning is value and to whom we assign is key. 

                        def myFun(**kwargs):
                                    for key, value in kwargs.items():
                                                print("%s == %s" % (key, value))


                        # Driver code
                        myFun(first='Geeks', mid='for', last='Geeks')

                        Output:

                        first == Geeks
                        mid == for
                        last == Geeks
                        
## Example 2:

Python program to illustrate  **kwargs for a variable number of keyword arguments with one extra argument. All the same but one change is we passing non keyword argument which acceptable by positional argument(arg1 in myFun). and kewword arguments we passing are acceptable by **kwargs. simple right?                        

                        def myFun(arg1, **kwargs):
                                    for key, value in kwargs.items():
                                                print("%s == %s" % (key, value))


                        # Driver code
                        myFun("Hi", first='Geeks', mid='for', last='Geeks')

                        Output:

                        first == Geeks
                        mid == for
                        last == Geeks
                        

## Using both *args and **kwargs to call a function

## Example 1:

Here, we are passing *args and **kwargs as an argument in the myFun function. By passing *args to myFun simply means that we passing the positinoal and varibale length arguments which is contained by args. so, “geeks” pass to the arge1 , “for” pass to the arg2 and “geeks” pass to the arg3. when we passing **kwargs as an argument to the myFun it’s mean that it accepts key-word arguments. Here, “arg1 is key and value is “Geeks” which is pass to arge1, and just like that “for” and “Geeks” pass to arg2 and arg3 respectively .After passing all the data we are printing all the data in lines. 

                        def myFun(arg1, arg2, arg3):
                                    print("arg1:", arg1)
                                    print("arg2:", arg2)
                                    print("arg3:", arg3)


                        # Now we can use *args or **kwargs to
                        # pass arguments to this function :
                        args = ("Geeks", "for", "Geeks")
                        myFun(*args)

                        kwargs = {"arg1": "Geeks", "arg2": "for", "arg3": "Geeks"}
                        myFun(**kwargs)

                        Output:

                        arg1: Geeks
                        arg2: for
                        arg3: Geeks
                        arg1: Geeks
                        arg2: for
                        arg3: Geeks
                        
## Example 2:

Here, we are passing *args and **kwargs as an argument in the myFun function. where ‘geeks’, ‘for’, ‘geeks’ is passed as *args, and first=”Geeks”, mid=”for”, last=”Geeks”  is pased as **kwargs and printing in the same line.                        

                        def myFun(*args, **kwargs):
                                    print("args: ", args)
                                    print("kwargs: ", kwargs)


                        # Now we can use both *args ,**kwargs
                        # to pass arguments to this function :
                        myFun('geeks', 'for', 'geeks', first="Geeks", mid="for", last="Geeks")

                        Output:

                        args: ('geeks', 'for', 'geeks')
                        kwargs {'first': 'Geeks', 'mid': 'for', 'last': 'Geeks'}                        

# __init__ in Python (__init__)

The __init__ function is called every time an object is created from a class. The __init__ method lets the class initialize the object's attributes and serves no other purpose. It is only used within classes
