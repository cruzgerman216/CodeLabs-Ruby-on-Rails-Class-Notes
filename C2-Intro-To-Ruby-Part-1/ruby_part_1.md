# Class 2 - The Ruby Programming Language Part 1
![Ruby Programming](../assets/images/C1-images/ruby.png)

Learning Ruby will be just as important as learning Rails. Understanding the basic syntax and programming fundamentals will be necessary before moving onto any technical framework. 

*Class 2 refers to videos 12 - 22 in Section 2: The Ruby Programming Language*

---

### Table of Contents 
- History of Ruby and Layer of Abstraction 
- Creating/Executing a Ruby file 
- Data Types
- Printing To The Terminal
- Variables
- Basic Methods
- String Expressions and Methods 
- Getting User Input 
- Operators 
- Project - Covid-19 Tracker and CLI  Part 1
---

## History of Ruby and Layer of Abstraction
Ruby was develop by a man named Yukihiro Matsumoto in Japan. He believed a layer of abstraction should be given to programming languages. What is meant by a layer of abstraction? Think about your phone. You use your phone for many reasons. It's easy to use and it's capable of doing much more than call and text. However, under the hood brings a layer of abstraction that is only understood by those who created it. 

Ruby is written in C. And even C is written in assembly language, which gets translated into machine code. It's this layer of abstraction that exists in the industry that allows for a much more efficent and faster way of developing tools such as Rails.

## Creating/Executing a Ruby file 
Create a new folder or create a newly fresh repl in [replit](https://replit.com/) called class 2. `NOTE: Any computers with ruby installed already is fine to work with. Be sure to create a file called main.rb. Replit will have this by default.` 

To create a ruby file, you must include the `.rb` extension to create ruby executable code. 

In order to execute the code in a Ruby file, enter the following to the terminal:

**terminal**
```
> ruby file_name.rb
```

## Data Types
Data types in programming are ways to define different types of data. A data type is also a piece of information that tells how the computer should interpret it. Each data type is used in a different manner. 

### Strings 
A string is a certain data type and is characterized by text. They are grouped by characters that can represent sentences and words. In order to define a string, you would need to use single quotes or double quotes outside of text.
```ruby
"This is an example. I can include any character, here is an example list: 1234567890!@#$%^&&*()"
```

Double quotes allow for single quotes whereas, single quotes cannot have within the string additional single quotes.

```ruby
"It isn't good to forget what a string represents." # Correct
'This isn't going to work.'                         # Error!
```

### Numbers
A number represents a range of digits. There are two types of Numbers: integers and floats. An integer is a whole number (no decimal) whereas floats have a decimal place. Numbers and mathematical calculations go hand and hand.

```ruby 
puts 1 # This is an Integer
puts 1.1 # And this is a Float
```
### Boolean
A boolean represents a unique value that is either `true` and `false`. We use booleans to solve problems that need logical solutions. We will explore booleans more when we talk about conditionals in the next class.

```ruby
true 
false 
```

### Hashes 
A hash is a series of unique key-value pairs. In order to access a value, square brackets are needed `[]` after the hash. In between the square brackets, include the name of the key as a symbol.

```ruby 
{:name => "John Doe", :age => 54}
{:name => "Amy Singer", :age => 43}[:name] # 'Amy Singer'
{name: "John Doe", name: "Amy Singer"} # Error! Duplicated key :name and overwritten
```

Here is a much newer syntax for defining key-value pairs for Ruby.

```ruby 
{name: "John Doe", age: 54}
{name: "Amy Singer", age: 43}[:name] # 'Amy Singer'
```

### Symbols 
Symbols are referred to as variable names in replacement to strings for higher performance rates. In order to define a symbol, use a colon `:`. Refer to hashes and the use of symbols to access values. 

```ruby
:name
```

### Arrays
An array is a list of data enclosed in square brackets `[]`. Each list item or element is separated by a comma `,`. An array can hold different amounts of data types. Here is an example: 

```ruby
[1, "string", true, :test, {name: "John Doe"}]
```

In an array, we aren't aware of the position of each element of the array (think of a thousand elements in a single array). To gain access of a value at a position, we use square brackets follow by a number. This number represents the position the element is at. The position of an array starts at 0 which in this case, will return `1`. Here is a demonstratin of accessing the first element of the array:

```ruby 
[1, "string", true, :test, {name: "John Doe"}][0] # 1
```

## Printing To The Terminal
When we print to the terminal, we allow ourselves as programmers to see the values in our code. We can choose what to output with three built in Ruby keywords: 
- `puts` 
- `print` 
- `p`

Type the following in main.rb: 
```ruby 
puts "Hello, World!"
```
 `puts` will print to the terminal whatever is in the righthand side of it, in this case `"Hello, World!"`. Execute the Ruby code, you should get the following: 

``` 
> Hello, world!
```

## Variables 
A variable contains data. Technically, the name of a variable references a specific memory location that contains data. To store a value in this memory location, we use the `=` sign. Keep in mind, whenever we store data in our computer's memory as the program runs, it is only temporary data. So when the program ends, the temporary data goes away.

```ruby 
message = "Hello, world!" 
```

Here we are storing the string `Hello, world!` in memory and reference that memory location with the variable name `message`. To read this line of code, we say `We set message to 'Hello, world!'`. You will also hear `We store 'Hello, world!' in message`. 

When a Ruby file get's executed, it is being read from top to bottom. Hypothetically speaking, if we set message to `"Another value!"` below line 1, the memory location that message references, will now contain "`Another value!`".

```ruby 
message = "Hello, world!"
puts message # Hello, world!
message = "Another value!"
puts message # Another value!
```
### Constant Variables 
In certain use cases, we like to make sure the value the variable contains will <em>not</em> change. To create this constraint, we can create what is called a constant variable. To create a constant variable, the variable name will be all uppercase.

```ruby 
API_KEY = "123456789"
```

In case we accidently change the value of this key, it will result in an error when compiled.

```ruby 
API_KEY = "123456789"
API_KEY = "abcdefghi" # ERROR, CANNOT CHANGE VALUE OF CONSTANT
```

### Naming Convention
When it comes to naming conventions, programming languages have their own style. In JavaScript, you should know variable names are `camelCase`. The common naming convention for Ruby variables, however, are `snake_case`. 

### Comments in Code
It's great to leave comments in code to remind yourself or other developers what the code does. To comment code out or to bypass the compiler, use the `#` sign. Everything after `#` will not get executed.

```ruby
# API KEYS
API_KEY = "12345678"

name = "John Doe" # This is an example
```
When this file gets compiled, anything after `#` that is on the same line will not be executed. 

## Basic methods
Methods are a set of pre-defined code lines or code block that have a purpose. This is a great way to resuse a piece of logic and separate our logic into defined methods. Ruby has built in methods in which we will explore later. For now, let's define our own method. To define a method, we must use keywords `def` follow by the method name in one line. In the next line, we must include the keyword `end`. `end` signifies the ending of the method or code block.

```ruby 
def method_name
end
```

In between `def` and `end`, we are able to execute a series of lines. The purpose of this method is to print `john doe` to the terminal.

```ruby 
def say_greetings_to
    puts "Greetings to John Doe"
end
```

However, when compiled, you will notice `John Doe` does not appear in the terminal? Why? That is because the method needs to be invoke or called to execute the series of lines say_greetings_to contains. You call invoke a method in two ways, with or without the opening and closing parenthesis after the method name.

```ruby 
say_greetings_to
say_greetings_to() # You can call a method like this as well
```

### Parameters in Methods 
Methods aren't always this static or 'never changing'. To make this dynamic, I like to pass in data into this method outside of the method definition. To reference this data, I will include what is called a parameter which will be called `name`. `name` is an alias and is a reference to data being passed in. When data is being passed into a method, it is referred to as an `argument`. Here, there is one argument being passing into say_greetings_to

```ruby 
def say_greetings_to(name)
    puts name
end

say_greetings_to "John Doe" # Prints John Doe
say_greetings_to("Amy Thompson") # Another way of passing data, prints Amy Thompson
```

### Multiple parameters
A method can have more than one parameter. To include more than one parameter, you must include a comma to separate each parameter. This rule also applies to multiple arguments. The order of arguments being placed is the order they will be referenced to by the method.


```ruby 
def say_greetings_to(name, friend)
    puts name
    puts friend
end

say_greetings_to "John Doe", "Amy Thompson"
say_greetings_to("John Doe", "Amy Thompson") 
# name will reference "John Doe"
# friend will reference "Amy Thompson"
```

### Local and Global Variables (Scope)
We've been using local variables since the beginning of the notes. To get more technical, when a local variable is defined, it can only be accessed in its scope. A scope defines where in the program a variable is accessible.

A local variable that is defined outside of, for example, a method, can only be accessed outside of the method.
`NOTE: KEEP IN MIND, SCOPE IS DIFFERENT FOR ALL LANGUAGES`

```ruby 
name = "John Doe" 
def my_name 
    puts name # Incorrect
end

my_name # Error! Undefined local variable or method 'name'
puts name # Prints John Doe
```

When a local variable is defined in a method, it can only be accessed within it's scope or in other words, it's method it was defined in. 

```ruby
def my_name 
    name = "John Doe"
    puts name # Correct
end 

name # Error! Undefined local variable or method 'name'
```

Global variables on the other hand, can be accessed throughout your entire file or files (depending if you load the file from another file). This is not recommended because somewhere in a file, you may have accidently used the same variable name and somewhere in the logic, can cause an issue. That avaiability and cause of security may be hard to keep track of. To declare a global variable you will need to place a `$` in front of a variable name. 

```ruby 
$name = "John Doe"

def my_name 
    puts $name 
end 

my_name # Correct
```

## String Expressions and Methods 
In programming, there will be times where a string value is not always static. We allow a string to be dynamic and change in value. To make a string dynamic, we can use what is called string concatenation.

### String Concatenation
String concatenation is the process in which multiple strings are joined together using the `+` operator.

```ruby 
name = "John Doe"
puts "My name is" + " " + name
```
### String Interpolation 
String interpolation is another way of joining multiple strings and values together. This is the preferred way.
```ruby 
name = "John Doe"
puts "My name is #{name}" # My name is John Doe
```
In between the opening `#{` and closing `}` will be evaluated as ruby code. 

### Built in String Methods 
Strings such as `"John Doe"` have what are called `built-in Ruby methods` and belong to the string class. A class is a template for objects. An object has what are called attributes and methods. Any string created has access to "instance methods" in the String class. [Here](https://ruby-doc.org/core-3.1.0/String.html) is a list of instance methods. On the left hand side, under **Methods** you will see a list categorized by `::` and `#`. In order to understand the documentation, methods that have `::`, are class variables. `#` are instance vaiables. Instances that belong to the String class such as "John Doe" or the name variable for short, will have access to any instance method. Here are some examples:

```ruby 
name = "John Doe"
puts name.class # Prints String
```
The built in string method `length` returns the number of characters in a string. Keep in mind, blank spaces count as a character.
```ruby 
name = "John Doe"
puts name.length # 8
```
You will occasionally see a `?` at the end of methods such as `empty?`. This will return a boolean. A boolean is a specific data type in which refers to two values 
```ruby 
true
false
```
Booleans are used for conditionals and will be further explained as we move along in the curriculum. `empty?` will check the number of characters in a string. If the string has no characters or the length is 0, then `empty?` will return true. Otherwise false.

```ruby 
    puts "John Doe".empty? # false
```

## Getting User Input 
Wouldn't it be more interesting if we were to get user input? Ruby already has these methods provided for Ruby Coders. To be able to do this, Ruby has given coders: `gets.chomp`.  Lets call this method. After doing so, you will notice your terminal will 'pause' and wait for a user input. Once you have entered a few characters, the IDE will continue executing the rest of the code.

```ruby 
puts "What's your name?" 
name = gets.chomp
puts "My name is #{name}."
```

## IRB
Ruby has a sandbox environment to execute Ruby code called IRB. This can be helpful for experimenting with different expressions. As someone who constantly moves from JavaScript to Ruby, I sometimes forget which methods or syntax belongs to what language. Occasionally, I use IRB to help solve this issue. To use IRB, you are going to want to go to a terminal and enter `irb`. It will then allow you to enter ruby code. 

**terminal**
```
irb
```

```
> puts "John Doe" # Enter
> John Doe # Result
```

To exit irb, you will need to enter `exit`. 
```
exit
```

## Operators

Occasionally in programming languages, there are operators that evaluate mathematical expressions. These operators are 
- `+` addition
- `-` subtraction
- `/` division
- `*` multiplication
- `%` Modulus - Will get the remainder from the division of two numbers
- `**` Exponent - Performs exponential calculation

Here's an example of how to use all operators.

```ruby 
# Expression |  # Result
# _______________________
puts 1 + 1      # 2
puts 1 - 1      # 0
puts 10 / 2     # 5
puts 5 * 5      # 25
puts 8 % 7      # 1
puts 4**3       # 64
```

### PEMDAS
PEMDAS is an acronynm that stands for `Parentheses Exponent Multiplication Division Addition Subtraction`. PEMDAS is a way to explain the order of operations when multiple operators are used in the same line. The same logic that applies in math applies in Ruby programming (and in most programming languages) as well. 

```ruby 
puts (4 - 4*2/4) + 10 # 12
```
---
## Project - Covid-19 Tracker and CLI  Part 1
With ruby fundamentals, we will have enough knowledge to create a Command Line Interface or CLI. A CLI is a program that runs in the terminal in which seeks user input. We will be building a CLI that includes data-scraping Covid-19 statistics. By scraping data, we will allow the user to choose which information they are more inclined to see. We will start by generating a gem.

### What is a gem?
Ruby [gems](https://rubygems.org/) are simply open source libraries that contain Ruby code. You can also think of gems as "tools" to be used. Here is a list of the most popular gems:
- With over 800 million downloads, [Bundler](https://rubygems.org/gems/bundler) tracks and manages all gem versions that are needed. Bundler is also used to create gems.
- [Minitest](https://rubygems.org/gems/minitest) provides a testing suit for your project applications
- By using [Nokogiri](https://rubygems.org/gems/nokogiri), you are easily able to scrape data from websites. 

### Creating a Gem
Let's go ahead and create our own gem. In this curriculumn, we won't be publishing the gem by any means but will give you a clear indication of how to create open source projects for other developers to use.

The project can be created in any ruby environment. We will be using Replit or a local IDE to create this project. In order to create a gem, we must use the bundler gem. To check whether or not you have bundler already installed, you can simply type the following in the command line:

``` 
bundler -v
```
In case you do not have Bundler installed, enter `gem install bundler`. 

Let's go ahead and generate a gem from scratch by entering the following in the command line:

```bundle gem USA_COVID_19_Tracker```<br>
### Gem setup
You will then get a series of questions. 
1. ```Do you want to generate tests with your gem?``` no <br>
We are not concern with testing our application at this moment of time. Of course, in future project we will welcome them.

2. ```Do you want to license your code permissively under the MIT license?``` yes <br>
By including an MIT license, you are allowing any developer to use the gem however they like as long as they give you credit. On another note, this furthers [open source development](https://blog.jcoglan.com/2010/08/15/what-i-mean-when-i-use-the-mit-license/).

```
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
documentation files (the “Software”), to deal in the Software without restriction, including without 
limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the
Software, and to permit persons to whom the Software is furnished to do so, subject to the following 
conditions...
``` 

3. ```Do you want to include a code of conduct in gems you generate?``` yes <br>


### Folder Structure 
After these series of questions, bundler will create a project folder with numerous of sub folders and files.

``` 
USA_COVID_19_Tracker
    bin 
    lib 
    .gitignore
    CODE_OF_CONDUCT.md
    USA_COVID_19_Tracker.gemspec
    gemfile
    gemfile.lock
    license.txt
    Rakefile
    README.md
```
Before explaining each folder and file, let's navigate to `USA_COVID_19_Tracker.gemspec`. Let's go ahead and make some adjustments. Please see the block below. We aren't concerned, at this moment of time, with the details of gem.

```ruby 
spec.summary = "" # SET TO EMPTY STRING
spec.description = "" # SET TO EMPTY STRING
spec.homepage = "" # SET TO EMPTY STRING
spec.license = "MIT"
spec.required_ruby_version = ">= 2.6.0"

spec.metadata["allowed_push_host"] = "" # SET TO EMPTY STRING

# comment out these lines of code
# spec.metadata["homepage_uri"] = spec.homepage
# spec.metadata["source_code_uri"] = ""
# spec.metadata["changelog_uri"] = ""
```

#### Bin Folder
Back to the folder structure. The bin folder contains two files: console and setup. Console is used for experimentation. Here you can test out specific features/code/issues. By executing this file, it will automatically start IRB. We can include variables, logic in our existing code to test the features. To execute this file, enter `bin/console`

```ruby
#!/usr/bin/env ruby
# frozen_string_literal: true

require "bundler/setup"
require "covid_19"

# You can add fixtures and/or initialization code here to make experimenting
# with your gem easier. You can also use a different console, if you like.

# (If you use this, don't forget to add pry to your Gemfile!)
# require "pry"
# Pry.start

require "irb"
IRB.start(__FILE__)
```

The setup file allows us to automate any other setup. 

#### Bin Folder
Mostly all of our file creation will happen in the bin folder. 

#### .gitignore
Files that are left untracked or "ignored" when committing code to GitHub.

#### gemfile
The gemfile is a list of gems that are necessary to execute the program. Soon, we will be adding more gems. 

### Getting Started
In the terminal, let's <em>**change directory**</em> or cd into the project folder. To check the available files and folders that exist in the current directory you are in, enter `ls`. Create a file called <em>**tracker.rb**</em> inside of the bin folder. Tracker will be our main executable file and act as our start place. 

### require 
We want to be able to use the contents of other files. To do that, we are going to use the built in ruby methods `require` and `require_relative`.

`require` and `require_relative` take in one argument string. This string represents a pathway to the specified file in the project folder. Both methods start at two different locations in the project folder. `require` starts at the **absolute path** or root directory. Let's use `require` to load <em>**bin/USA_COVID_19_Tracker.rb**</em>(USA_COVID_19_Tracker.rb is responsible for loading other files we will later create).

The creation of these strings are simliar to the linux commands in the terminal. When creating/reading a path, we start from left to right. 

**bin/tracker.rb**
```ruby 
require "./" # Starts at the root directory (USA_COVID_19_Tracker)
```
There are many folders (.git, bin, lib) and files (.gitignore, CODE_of_CONDUCT.md, ect) to load. We must first change directories to `lib` to get to `lib/USA_COVID_19_Tracker.rb`.

```ruby 
require "./lib" # Changes directory to the lib folder
```
Then load USA_COVID_19_Tracker.rb.

```ruby 
require "./lib/USA_COVID_19_Tracker.rb" # Loads specific file
```
Notice the usage of the forward slash `/` to change directories from the root directory to lib and to load USA_COVID_19_Tracker.rb.

### require_relative
When we execute tracker.rb in the terminal 

**terminal**
```
> ruby bin/tracker.rb
```
We get a file path error in USA_COVID_19_Tracker.rb.

```
`require': cannot load such file -- USA_COVID_19_Tracker/version
```

Navigate to lib/USA_COVID_19_Tracker.rb. Let's use `require_relative` to load the version file. The file that contains a `require_relative` line starts the path relative to where it is located. 

**USA_COVID_19_Tracker.rb**
```ruby 
 require_relative '' # USA_COVID_19_Tracker.rb is in the lib directory. This path starts at the lib directory. 
```
The lib directory contains the folder the version file exists in. Let's go ahead and change directories to the USA_COVID_19_Tracker folder.

```ruby 
require_relative 'USA_COVID_19_Tracker'
```
To load the file, let's include a forward slash `/` after the directory name.

```ruby 
require_relative 'USA_COVID_19_Tracker/version`
```

### Preparing the CLI class
Let's create a Ruby class. In ruby, classes contain methods and attributes. You can think of attributes as "variables" for classes. These methods and attributes are the blue print for creating objects. For example, strings are objects in which can invoke String methods ("example".length). We will dive deep into objects later.

Navigate to <em>**lib/USA_COVID_19_Tracker**</em> and create a file called <em>**cli.rb**</em>. To create a class, we must first start off with the keyword `class` follow by the classname. Let's call this class CLI.  At the end of every class contains the key word `end`. 

```ruby
class CLI
end
```

We want to eventually create a method from CLI to invoke the start of the application. Let's load this file in `lib/USA_COVID_19_Tracker.rb`.

**USA_COVID_19_Tracker.rb**
```ruby
require_relative "USA_Covid_19_Tracker/version"

module USACovid19Tracker
  class Error < StandardError; end
  # Your code goes here...
end

require_relative "USA_Covid_19_Tracker/cli.rb"
```
Tracker.rb will get the loaded content from this file. This also includes the CLI class. Navigate to `bin/tracker.rb`

### Creating a CLI instance
An instance is an object associated with a specific class. To create a CLI instance, we use the `new` method. 

**bin/tracker.rb**
```ruby
require_relative "../lib/USA_Covid_19_Tracker.rb"

CLI.new
```

### Adding methods to the CLI Class
Navigate to <em>**lib/USA_COVID_19_Tracker/cli.rb**</em>. In the scope of the cli class, between lines containing `class` and `end`, let's define what is referred to as an insance method. Call this method `run`.

**cli.rb**
```ruby
class CLI
    def run
    end
end
```

Navigate to <em>**tracker.rb**</em>. Because CLI.new is an instance of the CLI class, we can call an additional method onto it. Let's call the newly created `run` method.

**tracker.rb**
```ruby 
CLI.new.run # The start of the application
```
Navigate back to <em>**cli.rb**</em>. Let's define a new method that prints out a welcome mesage to the terminal!

**cli.rb**
```ruby
class CLI
    def run
    end

    def greeting
        puts "Welcome to the Covid-19 CLI Tracker!"
    end
end
```

Let's go ahead and include the `greeting` method inside of the `run` method. It's actually quite simple. All we have to do is include the name of the method we like to call in `run`. 

**cli.rb**
 ```ruby
class CLI
    def run
        greeting
    end

    def greeting
        puts "Welcome to the Covid-19 CLI Tracker!"
    end
end
```
Let's execute tracker.rb. 

**terminal**
```
> ruby bin/tracker.rb
> Welcome to the Covid-19 CLI Tracker!
```

Notice how every time tracker.rb gets executed, the terminal does not clear. Ruby gives us a neat method that allows us to clear the terminal at the start of the application. This method is called `system` which takes in 'clear' as the argument.

**cli.rb**
```ruby
class CLI
    def run
        system('clear')
        greeting
    end

    def greeting
        puts "Welcome to the Covid-19 CLI Tracker!"
    end
end
```

Let's create a new instance method called `end_program` that prints a farewell message for when the program stops executing. It makes sense to invoke this method at the end of the `run` method because it will be the last statement.

```ruby
class CLI
    def run
        system('clear')
        greeting
        end_program
    end

    def greeting
        puts "Welcome to the Covid-19 CLI Tracker!"
    end

    def end_program
        puts "See ya later!"
    end
end
```

### CLI menu
This CLI will have a menu. Define a method called `menu`. Let's use `gets.chomp` to get user input. 

**cli.rb**
```ruby 
    def menu
        input = gets.chomp.downcase
    end
```

Let's call menu between lines containing greeting and end_program.

**cli.rb**
```ruby
    def run
        system('clear')
        greeting
        menu  
        end_program
    end
```

Let's define another method called `list_options`. We will then use special syntax to print multiple lines without having to repeat `puts` more than once.

**cli.rb**
```ruby
    def menu
        list_options
        input = gets.chomp.downcase
    end
    def list_options
    # <<- targets a multi-line string with a reference of DOC (DOC is an alias)
    # gsub allows for pattern replacement and regular expressions
    # /^\s*/ means target every white space before the the first character
    # the '' means replace every white space with an empty quote
    # try replacing '' with '_'
        puts <<-DOC.gsub /^\s*/, '' 
            1.
            2.
            3.
            Which one would you prefer?
        DOC
    end
```

Let's now test this out. Enter the following 

**terminal**

ruby bin/tracker.rb
``` 
> Welcome to the Covid 19 tracker
> 1. 
> 2.
> 3. 
> Which one do you prefer?
> 1
> have a great day!
```
---

#### References
- https://launchschool.com/books/ruby/read/introduction
- https://en.wikipedia.org/wiki/Ruby_(programming_language)
- https://medium.com/@morgannegagne/what-is-a-ruby-gem-1eec2684e68
- https://bundler.io/
- https://stackoverflow.com/questions/21306512/difference-between-relative-path-and-absolute-path-in-javascript
- https://www.learneroo.com/modules/142/nodes/744
- https://www.techotopia.com/index.php/Ruby_Variable_Scope

Saw a mispelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 