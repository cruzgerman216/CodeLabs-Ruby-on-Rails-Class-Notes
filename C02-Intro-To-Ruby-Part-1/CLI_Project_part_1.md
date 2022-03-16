
# Project - Covid-19 Tracker and CLI  Part 1
With ruby fundamentals, we will have enough knowledge to create a Command Line Interface or CLI. A CLI is a program that runs in the terminal in which seeks user input. We will be building a CLI that includes data-scraping Covid-19 data. By scraping data, we will allow the user to choose which information they are more inclined to see. We will start by generating a gem.

---
### Table of Contents 
- <a href="#what-is-a-gem">What is a Gem?</a>
- <a href="#creating-a-gem">Creating a Gem</a>
- <a href="#gem-setup">Gem setup</a>
- <a href="#folder-structure">Folder Structure</a>
- <a href="#getting-started">Getting Started</a>
- <a href="#preparing-the-cli-class">Preparing the CLI Class</a>
- <a href="#cli-menu">CLI Menu</a>
--- 

<div id="what-is-a-gem"></div>

## What is a Gem?
Ruby [gems](https://rubygems.org/) are simply open source libraries that contain Ruby code. You can also think of gems as "tools" to be used. Here is a list of the most popular gems:
- With over 800 million downloads, [Bundler](https://rubygems.org/gems/bundler) tracks and manages all gem versions that are needed. Bundler is also used to create gems.
- [Minitest](https://rubygems.org/gems/minitest) provides a testing suit for your project applications
- By using [Nokogiri](https://rubygems.org/gems/nokogiri), you are easily able to scrape data from websites. 

<div id="creating-a-gem"></div>

## Creating a Gem
Let's go ahead and create our own Gem. In this curriculumn, we won't be publishing the gem by any means but will give you a clear indication of how to create open source projects for other developers to use.

The project can be created in any Ruby environment. We will be using Replit or a local IDE to create this project. In order to create a Gem, we must use the bundler Gem. To check whether or not you have bundler already installed, you can simply type the following in the command line:

``` 
bundler -v
```
In case you do not have Bundler installed, enter `gem install bundler`. 

Let's go ahead and generate a Gem from scratch by entering the following in the command line:

```bundle gem USA_COVID_19_Tracker```<br>

<div id="gem-setup">

## Gem setup
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

<div id="folder-structure"></div>

## Folder Structure 
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

### Bin Folder
Back to the folder structure. The bin folder contains two files: console and setup. Console is used for `experimentation`. Here you can test out specific features/code/issues. By executing this file, it will automatically start IRB. We can include variables, logic in our existing code to test the features. 

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

### Bin Folder
Mostly all of our file creation will happen in the bin folder. 

### .gitignore
Files that are left untracked or "ignored" when committing code to GitHub.

### gemfile
The gemfile is a list of gems that are necessary to execute the program. Soon, we will be adding more gems. 

<div id="getting-started"></div>

## Getting Started
In the terminal, let's <em>**change directory**</em> or cd into the project folder if you haven't already. To check the available files and folders that exist in the current directory you are in, enter `ls`. Create a file called <em>**tracker.rb**</em> inside of the bin folder. Tracker will be our main executable file and act as our start place. 

### require 
We want to be able to use the contents of other files. To do that, we are going to use the built in Ruby methods `require` and `require_relative`.

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
require "./lib/USA_COVID_19_Tracker.rb" # This line of code loads USA_COVID_19_Tracker.rb
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

<div id="preparing-the-cli-class"></div>

## Preparing the CLI class
Let's create a Ruby class. In Ruby, classes contain methods and attributes. You can think of attributes as "variables" for classes. These methods and attributes are the blue print for creating objects. For example, strings are objects in which can invoke String methods ("example".length).

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

require_relative "USA_Covid_19_Tracker/cli.rb" # Load cli.rb
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
CLI.new.run # Executes the run method in the CLI class
```
Navigate back to <em>**cli.rb**</em>. Let's define a new method that prints out a welcome mesage to the terminal.

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

Let's create a new instance method called `end_program` that prints a farewell message for when the program stops executing. It makes sense to invoke this method at the end of the `run` method because it will be the last message printed onto the terminal before ending the program.

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

<div id="cli-menu"></div>

## CLI menu
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
- [Next Part](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C03-Intro-To-Ruby-Part-2/CLI_Project_part_2.md)

---

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!
