# Class 4 - The Ruby Programming Language Part 3

*Class 3 refers to videos 33-43 in Section 2: The Ruby Programming Language*

---
### Table of Contents 
- <a href="#OOP">Object Oriented Programming (OOP)</a>
- <a href="#Classes">Classes</a>
- <a href="#Modules">Modules</a>
- <a href="#Project-Covid-19-Tracker-and-CLI-Part-3">Project - Covid-19 Tracker and CLI Part 3</a>
--- 

<div id="OOP"></div>

## Object Oriented Programming 
Object Oriented Programming is a programming paradigm used to battle against complex systems and prevent large scale errors. To do this, OOP disects an application's code into logical bundles. Unforuntely, we don't have enough time to cover all the concepts of OOP other than inheritance, I recommend you to explore some starting points under <a href="#topics-to-explore">Topics to Explore</a>.

<div href="Classes"></div>

## Classes
A class is a blueprint to create instances (objects). Objects are mistaken to be hashes. Keep in mind instances are created by a class. A class is defined by it's methods and attributes. To create a class, use the keyword `class` follow by the class name. Make 
sure the class name is always capitlized. To end the class block, use the keyword `end`.

```ruby 
class Animal 
end 
```
To create an instance, we will involve the class name as well as invoking the `new` method.

```ruby
Animal.new  # Creates an instance of the Animal class
```
### Instance Methods
This isn't very helpful as we see here, this class doesn't have any methods. Let's create an instance method. Instance methods are methods belonging to instances of the class. To define an instance method, we define the method in the class block.

```ruby 
class Animal
    def drinks
        puts "**drinks water**"
    end
end 

mammal = Animal.new 
mammal.drinks # Prints **drinks water**
```

### Class Methods 
We can also define class methods. In order to define a class method, include `self.` before the method name. 

```ruby 
class Animal
    def drinks
        puts "**drinks water**"
    end

    def self.from_the_class
        puts "Class method"
    end
end 
Animal.from_the_class # Prints Class Method
```

### Initialize Method 
The initialize method is a reserved method in Ruby classes. If you have dealt with contructors in other languages, you will exactly understand what the initialize method will do. The initialize method is part of the instance-creation process where attributes can be set or execute some form of logic.

```ruby 
class Animal
    def initialize()
        puts "Animal instance created!"
    end
end 
Animal.new # Prints Animal instance created!
```

By passing in arguments into the `new` method, the parameters that exist in initialize will reference these arguments.

```ruby
class Animal
    def initialize(type)
        puts "I am a #{type}!"
    end
end 
Animal.new("Dog") # Prints I am a dog!
```
### Attributes 
Methods in a class can be characterized by capabilities of the class or instance. However, we can store information to be kept within the class or instance using attributes. Attributes are a way to define a storage place. 

To create attributes, we need to define **getter** and **setter** methods. A `getter` method returns the value of the attribute and a `setter` sets the attribute to a value. 

### Instance Variables with getters and setters
Instance variables are attributes that exist for instances of the class. Let's create a `getter` and `setter` for an instance variable. Below I have defined an empty class called person.

```ruby 
def Person
end
```

To create a `setter`, we must define a method. The method name should be the name of the attribute follow by an `=` sign. We must also include a parameter.

```ruby 
def Person
    def name=(name)

    end
end
```

We aren't yet storing the value of the parameter. There are two ways we can store this value, in a instance variable or a class variable. An instance variable can be set or access by an instance. 

To create an instance variable, you must include `@` in front of the variable name.

```ruby 
def Person
    def name=(name)
        @name = name
    end
end
```

To create a getter, we will define the method to return the instance variable `@name`

```ruby 
def Person
    def name=(name)
        @name = name
    end
    
    def name
        @name
    end
end

person = Person.new
person.name = "John Doe"
person.name # returns "John Doe"
```

We can also use the initialize method to set an attribute. 

```ruby
class Person 
  def initialize(name)
    @name = name
  end 

  def name 
    @name
  end
end 

person = Person.new("John Doe")
puts person.name # John Doe
```

### Attribute Accessors
Instead of defining setters and getters, we can use attribute writers and readers. `attr_writer` and `attr_reader` will define the methods for us to read and set an instance variable. 

```ruby
class Person 
  attr_writer :age
  attr_reader :age
  def info 
    puts @age 
  end
end

person = Person.new 
person.age = 43
person.info() # prints 43
```

Without `attr_writer`, you will not be able to set the instance variable outside of the class (you can inside of the class). 

### attr_accessor, the best of both worlds
If you like to be able to set the instance variable and access it's value outside of the class, then you can use `attr_accessor`.

```ruby
class Person 
  attr_accessor :age

  def info 
    puts @age 
  end
end

person = Person.new 
person.age = 43
person.info() # prints 43
```


### Inheritance 
Inheritance is where a class inherits all the traits/behavior from another class. Here we have two classes: `Human` and `Coder`

```ruby 
class Human 
    def say(something)
        puts something
    end
end

class Coder 
end
```

We can say that a Coder inherits all of the Human traits because it too has a general trait like `say`. Let's have `Coder` inherit form `Human`. After `class Coder`, include the less than greater than sign `<` follow by `Human`. 

```ruby 
class Human 
    def say(something)
        puts something
    end
end

class Coder < Human
end

Coder.new.say("I'm a human too!")
```

<div id="Modules"></div>

## Modules
Modules are a great way to bundle commonly used methods for classes. Take these classes for example.

```ruby 
class Person 
end

class Animal
end
```

We can agree that a person and an animal both eat. They share that commonlity together but we don't necessarily want to create a class for this purpose. Modules sound perfect because you can't create instances with modules. When creating a module, start with the keyword `module` follow by the module name. To end the module definition, we use the keyword `end`.

```ruby 
module Eat 
    def eat(food)
        puts "**ate #{food}**"
    end
end
```

To include the module in these classes, use the `include` method follow by the module name. 

```ruby 
module Eat 
    def eat(food)
        puts "**ate #{food}**"
    end
end

class Person 
    include Eat
end

class Animal
    include Eat
end

# Creates a Person Instance and invokes/calls eat
Person.new.eat("Carrots") # Prints **ate Carrots**
```
Using the `include` method will add instance methods. If you like to add class methods, use `extend`.



#### References
- https://en.wikipedia.org/wiki/Nokogiri_(software)#:~:text=Nokogiri%20is%20an%20open%20source,from%20the%20rubygems.org%20repository.
- https://ruby-doc.org/stdlib-2.6.3/libdoc/open-uri/rdoc/OpenURI.html
- https://www.rubyguides.com/2019/02/ruby-class/


<def id="topics-to-explore"></div>

#### Topics to Explore 
- OOP Polymorphism
- OOP Encapsulation
- Modules as Namespaces
- Private vs protected vs Public 
- Super keyword
- Inheritance vs Modules

:wave: Saw a mispelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 