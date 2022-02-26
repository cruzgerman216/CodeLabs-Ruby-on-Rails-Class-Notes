# Class 2 - The Ruby Programming Language Part 1
![Ruby Programming](../assets/images/C1-images/ruby.png)

Learning Ruby will be just as important as learning Rails. Understanding the basic syntax and programming fundamentals will be necessary before moving onto any technical framework. 

*Class 2 refers to videos 12 - 22 in Section 2: The Ruby Programming Language*

---

### Table of Contents 
- <a href="#History-of-Ruby-and-Layer-of-Abstraction">History of Ruby and Layer of Abstraction<a>
- <a href="#Creating/Executing-a-Ruby-file">Creating/Executing a Ruby file<a>
- <a href="#Data-Types">Data Types<a>
- <a href="#Printing-to-the-Terminal">Printing to the Terminal<a>
- <a href="#Variables">Variables<a>
- <a href="#Basic-Methods">Basic Methods<a>
- <a href="#String-Expressions-and-Methods">String Expressions and Methods<a>
- <a href="#Getting-User-Input">Getting User Input <a>
- <a href="#Operators">Operators<a>
- <a href="#Project-Covid-19-Tracker-and-CLI-Part-1">Project - Covid-19 Tracker and CLI Part 1<a>
---

## History of Ruby and Layers of Abstraction
<div id="History-of-Ruby-and-Layer-of-Abstraction"></div>
Ruby was develop by a man named Yukihiro Matsumoto in Japan. He believed a layer of abstraction should be given to programming languages. What is meant by a layer of abstraction? Think about your phone. You use your phone for many reasons. It's easy to use and it's capable of doing much more than call and text. However, under the hood brings a layer of abstraction that is only understood by those who created it. 

Ruby is written in C. And even C is written in assembly language, which gets translated into machine code. It's this layer of abstraction that exists in the industry that allows for a much more efficent and faster way of developing tools such as Rails.

<div id="Creating/Executing-a-Ruby-file"></div>

## Creating/Executing a Ruby file 
Create a new folder or create a newly fresh repl in [replit](https://replit.com/) called class 2. `NOTE: Any computers with ruby installed already is fine to work with. Be sure to create a file called main.rb. Replit will have this by default.` 

To create a ruby file, you must include the `.rb` extension to create ruby executable code. 

In order to execute the code in a Ruby file, enter the following to the terminal:

**terminal**
```
> ruby file_name.rb
```

<div id="Data-Types"></div>

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

<div id="Printing-to-the-Terminal"></div>

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

<div id="Variables"></div>

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

<div id="Basic-Methods"></div>

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

<div id="String-Expressions-and-Methods"></div>

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

<div id="Getting-User-Input"></div>

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

<div id="Operators"></div>

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

#### References
- https://launchschool.com/books/ruby/read/introduction
- https://en.wikipedia.org/wiki/Ruby_(programming_language)
- https://medium.com/@morgannegagne/what-is-a-ruby-gem-1eec2684e68
- https://bundler.io/
- https://stackoverflow.com/questions/21306512/difference-between-relative-path-and-absolute-path-in-javascript
- https://www.learneroo.com/modules/142/nodes/744
- https://www.techotopia.com/index.php/Ruby_Variable_Scope

#### Topics to Explore 
- Type Conversions 
- Pass by Value or by Reference
- Primitive Types
- High Level Programming vs Low Level Programming 
- Assignment Operators 
- Parallel Assignment
- BitWise Operators
- Variable Scopes

Saw a mispelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 