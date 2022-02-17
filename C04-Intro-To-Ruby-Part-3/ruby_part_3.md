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

<div id="Project-Covid-19-Tracker-and-CLI-Part-3"></div>

## USA Covid CLI Tracker Part 3
<img src="https://miro.medium.com/max/636/0*08Uq7LNwpSfEA3zc.jpeg"/>

In Part 3, we will will focus primarily on setting up classes `Country` and `State`. We will also add gems `Nokogiri` and `open-uri`. These gems will be used to scrape info from a site and parse the HTML document. We ultimately want to print the info we scrape onto the console. Let's explore the webpage we like to scrape.

### Covid-19 USA Statistics Site
We will use this [site](https://www.worldometers.info/coronavirus/country/us/) to scrape data from. The way we will organize our data is to separate `countries` and `states`. We will use classes to do this.

### Preparing the Country Class
We will go ahead and use Ruby Classes to structure the Covid-19 data for countries and states. Create a file under lib called `country.rb`. Define a class called `Country`. 

```ruby
class Country 
end
```
Let's include attribute accessors for name, confirmed_cases, overall_deaths, and recoveries. 

```ruby 
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
end
```
To store Country Instances, declare a class variable and set it to an empty array.

```ruby 
@@Countries = []
```
Create a class method all that returns the @@Countries property.

```ruby
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
    @@Countries = []

    def self.all
        @@Countries
    end
end
```

Upon instantiation, we like to set the country attributes. To do this, define the intialize method. 

```ruby
    def initialize()
    end
```

Let's add parameters to `initialize` to set each instance variable upon instantiation. 

```ruby
    def initialize(name, confirmed_cases, overall_deaths, recoveries)
        @name = name
        @confirmed_cases = confirmed_cases
        @overall_deaths = overall_deaths
        @recoveries = recoveries
    end
```

### Basic Meta Programming using the built-in method send

Assigning values to each instance variable is redundant. Lets incorporate some meta programming. Meta programming can be referred to as "code that writes code for us". Include only one parameter called attributes. Get rid of the code lines within the method definition aswell.

```ruby
    # Attributes will be a hash
    def initialize(attributes)

    end
```
Let's use the hash method `each` to iterate through the hash. Include a one line code block `{}`. By using the method each, we can reference each key and it's value by coding the following: 

```ruby
    # Attributes will be a hash
    def initialize(attributes)
        attributes.each {|key, value| }
    end
```

In the one line statement, use `self` to invoke the `send` method. The send method will take in two arguments. The first will be the name of a method we like to execute. In this case, we like to execute a setter method for each attribute.

```ruby
    # Attributes will be a hash
    def initialize(attributes)
        attributes.each {|key, value| self.send("#{key}=") }
    end
```

Remember `attr_accessor` provides these methods for each instance variable. For example, here is the setter for the inatance variable `name`:

```ruby 
    def name=(value)
        @name = value;
    end
```

Notice how this method definition also includes a parameter. We can pass in the value as a second argument in `send`.
```ruby 
    def initialize(attributes)
    # Example:
    # new Country({:name=>"USA"})
        attributes.each {|key, value| self.send("#{key}=", value)}
    end
```

### Preparing the State Class
After defining the Country class, let's create a file called `state.rb` under lib and define the `State` class. Let's allow the `State` class to be the subclass/inherit from Country.

```ruby
class State < Country
    @@states = []
    def self.all
        @@states
    end
end
```

### Loading country.rb and State.rb
Include country.rb and state.rb in USA_Covid_19_Tracker.rb
```ruby
    require_relative "USA_Covid_19_Tracker/cli.rb"
    require_relative "./country.rb"
    require_relative "./state.rb"
```

This would be a good time to check if things work! :thumbsup:

### Scraping Setup
Create a file called scraper.rb under the lib directory. Include the file in the USA_Covid_19_Tracker.rb file

```ruby
require_relative "USA_Covid_19_Tracker/cli.rb"
require_relative "country.rb"
require_relative "state.rb"
require_relative "scraper.rb"
```

Define a class called Scrapper and include two class methods `scrape_usa` and `scrape_states`.


```ruby
class Scrapper

    def self.scrape_usa
    end

    def self.scrape_states
    end
end

```

Create a class constant and set it to the url we will be scraping from. A class constant cannot be reassigned.

```ruby
class Scrapper
    URL = "https://www.worldometers.info/coronavirus/country/us/" # class constant

    def self.scrape_usa
    end
```

### Setting up Open-URI and Nokogiri gems
In the gemfile, add gems Open-URI and Nokogiri. Open-URI allows you to make HTTP requests that will ultimately return a document of the webpage. Nokogiri will allow us to parse HTML in Ruby. 

```ruby
    git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

    # Specify your gem's dependencies in USA_Covid_19_Tracker.gemspec
    gemspec

    gem 'nokogiri', '~> 1.12', '>= 1.12.5'
    gem 'open-uri', '~> 0.1.0'
```
Run ```bundle install```

### Bundling Issue
You may get an error from your gemspec file

```The gemspec at /Users/username/Documents/GitHub/Ruby-GEM-CLI-Covid-Tracker/USA_Covid_19_Tracker/USA_Covid_19_Tracker.gemspec is not
valid. Please fix this gemspec.
The validation error was 'metadata['homepage_uri'] has invalid link: "TODO: Put your gem's website or public repo URL here."
```

Navigate to the Gemfile and comment out `gemspec`. We aren't interested at the moment to specify the dependencies of the program.


```ruby
# Specify your gem's dependencies in covid_19.gemspec
# gemspec
```

Run bundle install to install the necessary gems.

### Data Scraping

Parse html using Nokogiri and open-uri
At the top of the file, be sure to require both Nokogiri and open-uri
```ruby 
require 'nokogiri'
require "open-uri"
```
```ruby
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open(URL))
        puts doc
    end
```

Test this out and call this method 

```ruby
Scrapper.scrape_usa
```
run ```ruby lib/scraper.rb```

You should get something similiar to this. It should be a large document.
```
.
.
.
<div class="col-md-9" style="padding-left:0px;margin-left:0px;">
+ <input name="new_serious" type="number" onkeypress="return event.charCode &gt;= 48 &amp;&amp; event.charCode &lt;= 57" class="form-control case_number" id="new_serious">  =  <input onkeypress="return event.charCode &gt;= 48 &amp;&amp; event.charCode &lt;= 57" name="total_serious" type="number" class="form-control case_number" id="total_serious"> total serious  </div>
</div>
<div class="form-group row">
<div class="col-md-3">
<label for="total_tests" class="form_labels" style="line-height:34px;">Total Tests:</label>
</div>
<div class="col-md-9" style="padding-left:0px;margin-left:0px;">
<span style="color:#fff;">+</span> <input onkeypress="return event.charCode &gt;= 48 &amp;&amp; event.charCode &lt;= 57" name="total_tests" type="number" class="form-control case_number" id="total_tests">
.
.
.
```

1. In the scrape_usa method, use the css method to target specific classes of the document. This should return an array of text. Use the dev tools to inspect the document of what specific elements you are interested in. In this case, usa main data. This might be tricky to locate the css classes.

```ruby 
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        country_main = doc.css(".content-inner .maincounter-number")
```
keep printing each value as we add things little by little.

9. Store the first element. 

```ruby 
usa_confirmed_cases = country_main[0]
```

10. Use the text method to get the content of the element 

```ruby 
usa_confirmed_cases = country_main[0].text

```

11.  Use regex to get rid of every whitespace and comma. Then turn it into an interger.

```ruby
        usa_confirmed_cases = country_main[0].text.gsub(/[\s,]/,"").to_i
```

12. Do this repeatedly for the confirmed deaths and recoveries.

```ruby
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        country_main = doc.css(".content-inner .maincounter-number")
        usa_confirmed_cases = country_main[0].text.gsub(/[\s,]/,"").to_i
        usa_overall_deaths = country_main[1].text.gsub(/[\s,]/,"").to_i
        usa_recoveries = country_main[2].text.gsub(/[\s,]/,"").to_i
    end
```
Let's refactor this.

13. Create a ```concerns``` folder. Create a file called ```printable.rb```. In that, define a module called printable and in that module, define another module called styles.

```ruby
module Printable
    module Styles
    end
end
```

14. Define a method called text_to_integer. Use the logic we used in the scrape_usa data to do this.

```ruby
module Printable
    module Styles
        def text_to_integer(text)
            text == "" ? output = 0 : output = text.gsub(/[\s,]/,"").to_i
            output
        end
    end
end
```

15. Refactor the scraper class. Extend the printable module 

```ruby
class Scrapper
    extend Printable::Styles
```

16. Refactor the scrape_usa data

At the top of the file 
```ruby 
require_relative './concerns/printable.rb'
```
```ruby
        usa_confirmed_cases = text_to_integer(country_main[0].text)
        usa_overall_deaths = text_to_integer(country_main[1].text)
        usa_recoveries = text_to_integer(country_main[2].text)
    end
```

1.  Initialize a Country instance with this data
At the top of the file 
```ruby 
require_relative "./country.rb"
```
```ruby
        usa_recoveries = text_to_integer(country_main[2].text)
        usa = Country.new(name: "USA", confirmed_cases: usa_confirmed_cases, overall_deaths:usa_overall_deaths, recoveries:usa_recoveries)
    end
```


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