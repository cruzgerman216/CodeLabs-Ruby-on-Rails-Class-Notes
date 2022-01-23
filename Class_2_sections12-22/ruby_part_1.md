# Class 2 - The Ruby Programming Language
Learning Ruby will be just as important as learning Rails. Understanding the basic syntax and programming fundamentals will be necessary before moving onto any technical framework. 

## History of Ruby and Layer of Abstraction
Ruby was develop by a man named Yukihiro Matsumoto in Japan. He believed a layer of abstraction should be given to programming languages. What is meant by a layer of abstraction? Think about your phone. You use your phone for many reasons. It's easy to use and it's capable of doing much more than call and text. However, under the hood brings a layer of abstraction that is only understood by those who created it. 

Ruby is written in C. And even C is written in assembly lanuage, which gets translated into machine code. It's this layer of abstraction that exists in the industry that allows for a much more efficent and faster way of developing tools such as Rails.

## Printing to the terminal
Let's create a newly fresh repl in [replit](https://replit.com/) called class 2. `NOTE: Any computers with ruby installed already is fine to work with. Be sure to create a file called main.rb. Replit will have this by default.` 

Let's start out by printing to the terminal. When we print to the terminal, we allow ourselves as programmers to see the values in our code. We can choose what to output with three built in ruby keywords: 
- puts 
- print 
- p

Type the following in main.rb: 
```ruby 
puts "Hello, World!"
```
By default, replit will already have this coded for you in main.rb. `puts` will print to the terminal what is in the righthand side of it, in this case `"Hello, World!"`. Whatever is in double quotation marks is regarded as a string and is a specific data type. A data type is a piece of information that tells how the computer should interpret it. We will go over more data types soon. Futhermore, there are two ways to execute main.rb. 
1. You can click the `run` button in Replit and it will output the following in the console. 

```ruby 
Hello, world!
```
2. On another note, you can go to the shell or terminal and enter `ruby main.rb`. This will execute the code in main.rb. 

## Variables 


## Topics covered
- print values to console using print, p or puts
- methods (with arguments)
- string interpolation
- IRB
- data type methods 
  -  "test".class -> returns string, 
  -  "test".methods -> built in methods for strings
- escaping
- by value vs by reference


## USA Covid CLI Tracker Part 1
What is a gem? [source](https://medium.com/@morgannegagne/what-is-a-ruby-gem-1eec2684e68) <br>
They are simply open source libraries that contain Ruby code and are packaged with a little extra data. Using a gem allows a programmer to use the code within the gem in their own program, without explicitly inserting that code. Gems can be used for all sorts of purposes...Rails is a gem!

Let's create a gem that is a CLI (Command Line Interface) that scrapes covid-19 stats and prints them onto the console.

### Setup 

- Use an IDE such as VS Code
- In the console, enter
```bundle gem USA_COVID_19_Tracker```

```Do you want to generate tests with your gem?``` enter no <br>
```Do you want to license your code permissively under the MIT license?``` enter y <br>
```Do you want to include a code of conduct in gems you generate?``` enter y <br>

- cd into project folder
- create a file called tracker.rb under bin
NOTE: Tracker will act as our executable file

- add a print line and execute the following ```ruby bin/tracker.rb``` to the console to see if it works.

- require the file path to 'lib/USA_Covid_19__Tracker.rb" to your tracker.rb file.
```ruby
require_relative "../lib/USA_Covid_19_Tracker.rb"
```
NOTE: In lib/USA_Covid_19_tracker.rb, this will act as our root module of imports

### Preparing the CLI class

- under lib/USA_Covid_19_Tracker, create a file called cli.rb
- Create a class called CLI
- Define a method called test. In test, print out a statement.

```ruby
class CLI
    def test
        puts "test"
    end
end
```

- Under lib, in USA_Covid_19_Tracker.rb, require cli.rb so that our tracker file can use it

```ruby
require_relative "USA_Covid_19_Tracker/version"

module USACovid19Tracker
  class Error < StandardError; end
  # Your code goes here...
end


require_relative "USA_Covid_19_Tracker/cli.rb"
```

- Under bin, in tracker, instantiate an instance from the CLI class and call out the test method

```ruby
require_relative "../lib/USA_Covid_19_Tracker.rb"

CLI.new.test
```

- run ```ruby bin/tracker.rb``` in the console

- Instead of calling the test method, let's call run instead

```ruby 
CLI.new.run
```

### CLI class
- define a method named run and greeting
- print out a welcome statement in the greeting method

```ruby
class CLI
    def run
    end

    def greeting
        puts "Welcome to the Covid-19 CLI Tracker!"
    end
end
```

- in run, include system("clear") to clear the console when the program starts
- call the greeting method after system("clear")

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

- define another method called end_program
- in the program, print out "See ya latter!"
- Inlude it in the run method

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

- define a method called menu
- store user input in a variable called input
- call menu inside of the run method between greeting and end_program

```ruby
    def run
        system('clear')
        greeting
        menu  # <------ here
        end_program
    end
.
.
.
    def menu
        input = gets.chomp.downcase
    end
```

- define a method called list_options
  - use <<- and regex to print a multi-line string
- call list_options in menu

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

enter ```ruby bin/tracker``` in the terminal

#### References
- https://launchschool.com/books/ruby/read/introduction
- https://en.wikipedia.org/wiki/Ruby_(programming_language)
- 