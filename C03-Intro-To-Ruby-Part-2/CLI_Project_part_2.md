# USA Covid CLI Tracker Part 2
As of now, the CLI exits after input. Let's incorporate a while loop to prevent this from happening. 

---
### Table of Contents 
- <a href="#Implementing-a-Loop">Implementing a Loop</a>
- <a href="#Choosing-Options-with-case/when">Choosing Options with case/when</a>
--- 

<div id="Implementing-a-Loop"></div>

## Implementing a Loop
Return the user input in the `menu` method. 

```ruby
    def menu
        list_options
        input = gets.chomp.downcase
        return input;
    end
```

In the `run` method, create a `while` loop. As it's condition, check to see if user input is 'exit'. If so, end the while loop. 
```ruby
    def run
        system('clear')
        greeting
        while menu != 'exit' # Will always run as long as the user does not enter 'exit'
        end
        end_program
    end
```

<div id="Choosing-Options-with-case/when"></div>

## Choosing Options with case/when

The user can see the list but the CLI does not react to the chosen option. Let's create a `case/when` block. Create a method `choose_option` that accepts one parameter. Define the `when` blocks. 

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

Include `choose_option` within the `menu` method. Be sure to pass in the input as an argument.
```ruby
   def menu
        list_options
        input = gets.chomp.downcase
        choose_option(input)
        return input;
    end
```

Go head and test this.

**Terminal**
```
> Welcome to the Covid 19 tracker
> 1. 
> 2.
> 3. 
> Which one do you prefer?
> 1
> Number 1 chosen
> 1. 
> 2.
> 3. 
> Which one do you prefer?
> exit
> have a great day!> 
```

--- 
- [Next Part](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C04-Intro-To-Ruby-Part-3/CLI_Project_part_3.md)

---

Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 