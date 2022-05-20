# Class 3 - The Ruby Programming Language Part 2
Now we get into problem solving that involves even the basic features of most programming languages. 

*Class 3 refers to videos 23 - 32 in Section 2: The Ruby Programming Language*

---
### Table of Contents 
- <a href="#Return-Expressions-In-Methods">Return Expressions In Methods</a>
- <a href="#Conditionals">Conditionals</a>
- <a href="#Case-Statements">Case Statements</a>
- <a href="#Arrays">Arrays</a>
- <a href="#Loops">Loops</a>
- [Project - Covid-19 Tracker and CLI Part 2](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C03-Intro-To-Ruby-Part-2/CLI_Project_part_2.md)
--- 

<div id="Return-Expressions-In-Methods"><div>
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

<div id="conditionals"></div>

## Conditionals 
Conditionals are one of the most important fundamental blocks in programming. They are logic solvers. Think about it like this, let's say your credit card limit is at 2,000 dollars. If you go pass your credit car limit, it will decline. If you go lower than 2,000 dollars, then you are fine! Let's code this using conditionals! See below the starter code

```ruby 
purchase = 500
# When purchase is greater than 2000, print "Insufficient funds"
# When purchase is less than 2000, print "Successful transaction"
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
# When purchase is less than 2000, print "Successful transaction"
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
    puts "Will not print because the previous logic block is true"
end 
```

Because the compiler reads from top to bottom, the if block is satisfied once one conditional is true. Once satisfied, it will disregard other conditionals.

### Comparison & Logical Operators
How do we check to see if purchase is greater than 2000? We use what are called comparison operators. Comparison operators are the process in which compares two values in a certain fashion and result in either `true` or `false`. Here is a list of comparison operators. 

- `<`         Less than 
- `>`         Greater than
- `<= `       Less than or equal to
- `>=`        Greater than or equal to
- `==`        Equal to
- `!=`        Does not equal

A logical operator check for two or more comparisons.

- `&&`        And checks two conditions, **both** must be true to execute the logic block
- `||`        Or checks two conditions, **at least one** must be true to execute the logic block

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

Let's include another condition that checks if the purchase is greater than 1500, and less than 2000. We will use the logical operator `&&` to see if both conditions are true, if so, return true.

```ruby 
if purchase > 1500 && purchase < 2000 # false
    puts "Almost out of funds"
elsif purchase < 2000   # true 
    puts "Successful transaction"
elsif purchase > 2000 # false 
    puts "Insufficient funds"
end 
```

<div id="Case-Statements"></div>

## Case Statements 
When deciding to use if/else conditionals, you may want to consider case statements as they are similar. Case statements start with the keyword `case` follow by a value. This value is check by each line containing `when` keywords. Each `when` line is followed by a value. If the value matches, the code block will get executed.

```ruby
name = "Delilah"
case name
when "Delilah"
    puts "Found Delilah!" # Prints
when "Noah"
    puts "Found Noah!"
else 
  puts "Could not find anyone"
end 
```

<div id="Arrays"></div>

## Arrays
 An array is a list of items(strings, integers, arrays, objects, ect)

```ruby 
[1, '4', true, {name: "John"}]
```

### Pop
Invoking the array method `pop` will remove the last element of the array.

```ruby 
names = ["John", "Sam", "Amy"]
names.pop # returns removed element "Amy"
puts names # ["John", "Sam"]
```

### Push
Invoking the array method `push` will add an element at the end of the array.

```ruby 
names = ["Isaiah", "Delilah", "Alexis"]
names.push("Noah") # returns ["Isiah", "Delilah", "Alexis", "Noah"]
```

### Join
Invoking the array method `join` will combine all elements as a string. 

```ruby 
names = ["My", "name", "is", "Skylar"]
names.join # "MynameisSkylar"
```
You can also include a string as an argument to put in between each element.
```ruby 
names = ["My", "name", "is", "Skylar"]
names.join(" ") # "My name is Skylar"
```

### Each
Invoking the array method `each` will allow us to iterate through the list of elements in a code block using keywords `do` to start the block and `end` to end the block. `|reference|` is place after `do` as a reference to each element of the array. The code block is repeated as many times as there are elements in the array.

```ruby 
names = ["Isaiah", 'Alexis', 'Emily']

names.each do |name|
    # name is a reference to each element of the array 
    puts "My name is #{name}."
end 
```
**Terminal**
```
> My name is Isaiah
> My name is Alexis
> My name is Emily
```

Instead of a code block, we can also format the iteration as an in-line block. 
```ruby
names.each {|name| puts "My name is #{name}}
```

<div id="Loops"></div>

## Loops 
Loops are a fundamental feature in programming languages. Loops allow a code block to be repeated as long as a condition is met. 

### while loop
A `while` loop checks a condition before running it's code block. As long as the condition is true, it will repeat. `NOTE: It's common to create an infinite loop on accident. You have to make sure the loop will eventually end as an infinite loop will crash the program.` To define a `while` loop, use the keyword `while` follow by the condition. Use the keyword `end` to end the code block.

```ruby
i = 0
while i < 5
  puts i
  i += 1
end 
```

**Terminal**
```
> 0
> 1
> 2
> 3
> 4
```
### for loop
A for loop is preferred when the number of iterations are known. 

```ruby 
for a in 0..4 do
    puts a  
end
```

### do..while loop
A `do..while` is similar to the while loop, however, it executes the code block at least once.

```ruby
i = -100
loop do
    puts "Example"
    if i < 0
        break # stops the loop
    end
end
```
### until loop
The `until` loop will repeatedly execute the code block until the condition has been met.

```ruby 
i = 0
until i == 5 do 
  puts i 
  i = 1 + i
end
```
**terminal**
```
> 0
> 1
> 2
> 3
> 4
```

## Covid-19 CLI Part 2
- [Project - Covid-19 Tracker and CLI Part 2](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C03-Intro-To-Ruby-Part-2/CLI_Project_part_2.md)

---

#### References
- https://www.tutorialspoint.com/ruby/ruby_methods.html
- https://www.geeksforgeeks.org/ruby-loops-for-while-do-while-until/

#### Topics to Explore 
- Case/When
- The Call Stack 
- Infinite Loops
- Recursion

--- 
- [Next Class](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C04-Intro-To-Ruby-Part-3/ruby_part_3.md)

Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 
