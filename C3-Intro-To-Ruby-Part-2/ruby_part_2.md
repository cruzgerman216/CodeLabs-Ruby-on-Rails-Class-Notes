# Class 3 - The Ruby Programming Language Part 2
Now we get into problem solving that involves even the basic features of most programming languages. 

*Class 3 refers to videos 23 - 32 in Section 2: The Ruby Programming Language*

---
### Table of Contents 
- Methods
- Conditionals
- Arrays
- Loops
- USA Covid CLI Tracker Part 2
--- 
<!-- - methods
  - Ruby methods are very similar to functions in any other programming language. Ruby methods are used to bundle one or more repeatable statements into a single unit.
- if/else conditions
  - if/elses statements are logic blocks. Executes code if the conditional is true. If the conditional is not true, code specified in the else clause is executed.
- array
  - An array is a list of items(strings, integers, arrays, objects, ect)
- hash
  - Hash is a collection of unique keys and their values
- While loop
  - A "While" Loop is used to repeat a specific block of code an unknown number of times, until a condition is met. -->


## Methods 

## Conditionals 

## Array 

## Hash 

## Loops 

## USA Covid CLI Tracker Part 2

### While loop

- return user input in menu method

```ruby
    def menu
        list_options
        input = gets.chomp.downcase
        return input;
    end
```

- In the run method, use a while loop to conditionally execute a block of code unless the user enter's 'exit'

```ruby
    def run
        system('clear')
        greeting
        while menu != 'exit'
        end
        end_program
    end
```

Test the cli. 

- Define a choose_option method with a parameter. This method should execute code in a set of case when statements corresponding to the user's input. 

```ruby
    def choose_option(option)
        case option
        when "1"
            puts "Number 1 chosen"
        when "2"
            puts "Number 2 chosen"
        end
    end
```

- call choose_option in the menu method
```ruby
   def menu
        list_options
        input = gets.chomp.downcase
        choose_option(input)
        return input;
    end
```

- Show https://www.worldometers.info/coronavirus/country/us/ to scrape data from

### Preparing the country and state classes

- Create a file under lib called country.rb
- define a class called Country
  - A class a set of instructions of what an instance will ook like

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
#### References
- https://www.tutorialspoint.com/ruby/ruby_methods.html
- https://www.geeksforgeeks.org/ruby-loops-for-while-do-while-until/
- Send method: https://stackoverflow.com/questions/3337285/what-does-send-do-in-ruby