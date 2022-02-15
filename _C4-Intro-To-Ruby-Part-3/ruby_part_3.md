# Class 4 - The Ruby Programming Language Part 3

*Class 3 refers to videos 33-43 in Section 2: The Ruby Programming Language*

---
### Table of Contents 
- Object Oriented Programming (OOP)
- Classes
  - Initialize method
  - attr_accessor, attr_reader, attr_writer
- Modules
- Project - Covid-19 Tracker and CLI  Part 3
--- 

### Object Oriented Programming 
Object Oriented Programming is a programming paradigm used to battle against complex systems and prevent large scale errors. To do this, OOP disects an application's code into logical bundles.

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



## USA Covid CLI Tracker Part 3

### Covid-19 USA Statistics Site
We will use this [site](https://www.worldometers.info/coronavirus/country/us/) to scrape data from. The way we will organize our data is to separate countries and states. We will use classes to do this.

### Preparing the Country and State classes
We will go ahead and use Ruby Classes to structure the Covid-19 data for countries and states. 

Create a file under lib called `country.rb`. Define a class called `Country`. 

```ruby
class Country 

end
```


```ruby 
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
end
```
- declare attribute accessors name, confirmed_cases, overall_deaths and recoveries
- attribute accessors give getters and setters to these properties so you can access them
```ruby 
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
end
```

- Create a property called @@Countries and set it to an empty array
```ruby 
@@Countries = []
```
- Create a class method all that returns the @@Countries property
    - a class method can only be called from the Class itself. Instances do not have access to this.
```ruby
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
    @@Countries = []

    def self.all
        @@Countries
    end
end
```

- create an initialize method(acts as a constructor in other languages like JavaScript) that allows you to set your attribute accessors with metaprogramming
  - instead of multiple lines we can use the each methd to access these key paired values
  - In the statement, with the self method use the send method
  - The first argument in send() is the message that you're sending to the object - that is, the name of a method. It could be string or symbol but symbols are preferred. Then arguments those need to pass in method, those will be the remaining arguments in send()
```ruby 
    def initialize(attributes)
    # Example:
    # new Country({:name=>"USA"})
        attributes.each {|key, value| self.send("#{key}=", value)}
    end
```

- Create a separate file called state.rb under lib
    - define a class called state that inherits Country
      - By inheriting Country, we get inherit attributes from the country class and the initialize method
    - defined class property called @@states set to an empty array
    - define a class method called all that returns @@states

```ruby
class State < Country
    @@states = []
    def self.all
        @@states
    end
end
```

- include country.rb and state.rb in USA_Covid_19_Tracker.rb
```ruby
    require_relative "USA_Covid_19_Tracker/cli.rb"
    require_relative "./country.rb"
    require_relative "./state.rb"
```

- test the cli to see if it works still

### Scraping Setup

1. create a file called scraper.rb under the lib directory. Include the file in the USA_Covid_19_Tracker.rb file

```ruby
require_relative "USA_Covid_19_Tracker/cli.rb"
require_relative "./country.rb"
require_relative "./state.rb"
require_relative "./scraper.rb"
```
2. define a class called Scrapper and include two class methods scrape_usa and scrape_states


```ruby
class Scrapper

    def self.scrape_usa
    end

    def self.scrape_states
    end
end

```

3. Create a attribute and set it to the url we will be scraping from

```ruby
    URL = "https://www.worldometers.info/coronavirus/country/us/"
```

4. In the gemfile, add gems nokogiri and open-uri
Nokogiri is an open source software library to parse HTML and XML in Ruby. OpenURI is an easy-to-use wrapper for Net::HTTP, Net::HTTPS and Net::FTP.
```ruby
    git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

    # Specify your gem's dependencies in USA_Covid_19_Tracker.gemspec
    gemspec

    gem 'nokogiri', '~> 1.12', '>= 1.12.5'
    gem 'open-uri', '~> 0.1.0'
```
5. run ```bundle install```

You may get an error from your gemspec file
```The gemspec at /Users/username/Documents/GitHub/Ruby-GEM-CLI-Covid-Tracker/USA_Covid_19_Tracker/USA_Covid_19_Tracker.gemspec is not
valid. Please fix this gemspec.
The validation error was 'metadata['homepage_uri'] has invalid link: "TODO: Put your gem's website or public repo URL here."```

- comment out this section in your gemspec file 

```ruby
  # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
  # to allow pushing to a single host or delete this section to allow pushing to any host.
 
  # if spec.respond_to?(:metadata)
  #   spec.metadata["allowed_push_host"] = "TODO: Set to 'http://mygemserver.com'"

  #   spec.metadata["homepage_uri"] = spec.homepage
  #   spec.metadata["source_code_uri"] = "TODO: Put your gem's public repo URL here."
  #   spec.metadata["changelog_uri"] = "TODO: Put your gem's CHANGELOG.md URL here."
  # else
  #   raise "RubyGems 2.0 or newer is required to protect against " \
  #     "public gem pushes."
  # end
```

set these attributes to an empty string
```ruby
 spec.summary       = ""
  spec.description   = ""
  spec.homepage      =  ""
  spec.license       = "MIT"
```
You may also get a bundler issue 
 change the version to what you have, I have version 2.2.32

 ```ruby
spec.add_development_dependency "bundler", "~> 2.2.32"
  spec.add_development_dependency "rake", "~> 10.0"
  ```

run bundle install to install the necessary gems


6. Parse html using Nokogiri and open-uri
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

7. test this out and call this method 

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

8. In the scrape_usa method, use the css method to target specific classes of the document. This should return an array of text. Use the dev tools to inspect the document of what specific elements you are interested in. In this case, usa main data. This might be tricky to locate the css classes.

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


#### Topics to Explore 
- OOP Polymorphism
- OOP Encapsulation
- 

Saw a mispelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 