# Class 3 - The Ruby Programming Language Part 2
Now we get into problem solving that involves even the basic features of most programming languages. 

*Class 3 refers to videos 23 - 32 in Section 2: The Ruby Programming Language*

---
### Table of Contents 
- Return Expressions In Methods 
- Conditionals
- Arrays
- Loops
- USA Covid CLI Tracker Part 2
--- 

## Return Expressions in Methods 
In class 2, we introduced methods. To jog your memory, Ruby methods are used to bundle one or more repeatable statements into a single unit. The last line of every Ruby method gets return. This gives the opportunity to return an evaluated expression and store it in a declared variable.

```ruby
def add(num_one, num_two)
    puts "Adding numbers..."
    num_one + num_two # Last line that will be return
end

result = add(5, 5) 
puts result # prints 10
```

You can also use the `return` keyword to explicitly return a value. Anything after the line of code containing the `return` keyword will not execute.

```ruby 
def add (num_one, num_two)
    return num_one + num_two 
    puts "test" # Will not execute after return
end

result = add(5,5)
puts result # prints 10
```

## Conditionals 
Conditionals are one of the most important fundamental blocks in programming. They are logic solvers. Think about it like this, let's say your credit card limit is at 2,000 dollars. If you go pass your credit car limit, it will decline. If you go lower than 2,000 dollars, then you are fine! Let's code this using conditionals! See below the starter code

```ruby 
purchase = 500
# When purchase is greater than 2000, print "Insufficient funds"
# When purchase is less than 2000, print "Successfull transaction"
```

### If/else/elsif/end
In order to use a conditional, we must use keywords `if` and `end`. After the keyword `if` lies a boolean. If the boolean is `true`, the statements between lines `if` and `end` will execute. If it is `false`, it will **not** execute the block of code. 

```ruby 
purchase = 500
# When purchase is greater than 2000, print "Insufficient funds"
if true
    puts "Will print!"
end 

if false 
    puts "Will not print!!"
end 
# When purchase is less than 2000, print "Successfull transaction"
```

We can also refer to an `else` statement to execute if the conditional is false. 

```ruby 

if false 
    puts "Will not print"
else 
    puts "Will print"
end 
```

Chaining conditionals is quite frequent. To do so, we use the keyword `elsif`
```ruby 

if false 
    puts "Will not print"
elsif true 
    puts "Will print"
else 
    puts "Will print"
end 
```

Because the compiler reads from top to bottom, whichever conditional that is `true` first, the following logical block will get executed.

### Logical Operators
How do we check to see if purchase is greater than 2000? We use what are called logical operators. Logical operators are the process in which compares two values in a certain fashion and result in either `true` or `false`. Here is a list of logical operators. 

- <         Less than 
- >         Greater than
- <=        Less than or equal to
- >=        Greater than or equal to
- ==        Equal to
- !=        Does not equal
- &&        And checks two conditions, **both** must be true to execute the logic block
- ||        Or checks two conditions, **at least one** must be true to execute the logic block

Use the 'less than' sign or `<` to check to see if purchase is less than 2000.

```ruby 
purchase = 500
if purchase < 2000 # true 
    puts "Successful transaction"
end 
```

Use the greater than sign or `>` to check to see if purchase is greater than 2000.

```ruby
if purchase < 2000 # true 
    puts "Successful transaction"
elsif purchase > 2000 # false 
    puts "Insufficient funds"
end 
```

Let's include another condition that checks if the purchase is greater than 1500, and less than 2000. `Let's include this at the top level of the conditional chain to be read first otherwise, the other conditionals we get executed first.`

```ruby 
if purchase > 1500 && purchase < 2000 
    puts "Almost out of funds"
elsif purchase < 2000   # true 
    puts "Successful transaction"
elsif purchase > 2000 # false 
    puts "Insufficient funds"
end 
```

<!-- - array
  - An array is a list of items(strings, integers, arrays, objects, ect)
- hash
  - Hash is a collection of unique keys and their values
- While loop
  - A "While" Loop is used to repeat a specific block of code an unknown number of times, until a condition is met. --> -->

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