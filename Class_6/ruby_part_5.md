# The Ruby Programming Language

## Topics covered
- **Authenticatation systems**
  - Passwords are vulnerable to hackers. Passwords are never stored as just strings in the database. There are tools that allow you to encrypt passwords to prevent security vulnerabilities. An example of a tool is Bcrpyt.
- **Bcrpyt**
  - bcrypt() is a sophisticated and secure hash algorithm designed by The OpenBSD project for hashing passwords. The bcrypt Ruby gem provides a simple wrapper for safely handling passwords.
- **Modules**
  - In Ruby, modules are somewhat similar to classes: they are things that hold methods, just like classes do. However, modules can not be instantiated. I.e., it is not possible to create objects from a module. And modules, unlike classes, therefore do not have a method new .
## USA Covid CLI Tracker Part 5

### Scraping state statistics

1. In the scraper.rb file, inside of the class method scrape_states, scrape all 50 states from the provided URL. Inspect the elements from the page we are scraping and indicate the specific elements we want to scrape data from.
```ruby
    def self.scrape_states
        puts "Scraping States stats"
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        states_table = doc.css('tbody tr')
    end
```

- Inside the scrape_usa method, print to the console "Scraping USA stats"

1. Iterate through from 1 to 51 of the array (skipping 0 due to it being the column headers). And get the content from each element 
```ruby

        states_table[1..51].each do |row|
            name = row.css('td')[1].text.split(" ").join(" ")
            cases = text_to_integer( row.css('td')[2].text)
            deaths = text_to_integer(row.css('td')[4].text)
            recoveries = text_to_integer(row.css('td')[6].text)
        end
```

3. Create a state instance when iterating through the states_table array and store pass in the corresponding values. Check to see if the name equals "Distrct Of Columbia". If so, skip it.
**EXPLANATION: We want to ultimately save the instances in the State class so that our other classes have access to it. We still need the logic to do this so we will do this shortly.**
```ruby
            deaths = text_to_integer(row.css('td')[4].text)
            recoveries = text_to_integer(row.css('td')[6].text)
            if name != "District Of Columbia"
                State.new(name: name, overall_deaths:deaths, confirmed_cases: cases, recoveries:recoveries)
            end
```

### Creating the Grabable module
**EXPLANATION: The grabable module is responsible for defining methods other classes can use. In this case, I want to create a save method that allows instances to be "saved" under their class.**

4. Create a grabable.rb file under the concerns directory

5. In the file, define a module called Grabable. inside Grabable. Define another module called ClassMethods.
**EXPLANATION: Reason I want to include a module within a module is because Grabable can contain multiple logical methods that can bundled into modules. In this case, ClassMethods module, although they are generic, are for class methods.**

```ruby
module Grabable
    module ClassMethods
    end
end
```

6. In the ClassMethods module, define an instance method that will store the instance in the class attribute @@all. Use the all method as it returns @@all.

```ruby
        def save 
            self.class.all << self 
        end
```
- Self refers to the instance that calls save.
- self.class refers to the instance class
-  double < signs is another way of adding data to an array. It's equalivent to the pushing method in JavaScript.

7. After pushing each instance, use the built in array method sort the instances by name alphabetically. 

```ruby
module Grabable
    module ClassMethods
        def save 
            self.class.all << self 
            all = self.class.all.sort {|a,b| a.name <=> b.name}
        end
    end
end
```

### Confuring country.rb and state.rb with the ClassMethods module.
8. In the country.rb file, include the ClassMethods methods in the Country class

```ruby 
    require_relative "./concerns/grabable.rb"
    class Country
    include Grabable::ClassMethods
```

9. In the initialize method, include the save method.
```ruby
    def initialize(attributes)
        attributes.each {|key, value| self.send("#{key}=", value)}
        save
    end
```
10. In the state.rb file, define an intialize method and include the super keyword

```ruby
    @@states = []
    
    def initialize(attributes)
        super
    end
```
#### CLI/Scraping setup
11. In the cli.rb, define a method called scrape. Call scrape_use and scrape_states from the Scrapper class
```ruby
    def scrape
        Scrapper.scrape_usa
        Scrapper.scrape_states
    end
```

12. Call the scrape method at the top of the run method

```ruby
    def run
        scrape
        system('clear')
        CRUD.create_secure_users(User.all)
```

#### Adding logic to choose_menu
13. In the choose_option menu, under the when block, print out all the instances the State class holds in the @@all attribute for when users choose option 1 in the menu.

```ruby 
        when "1"
            State.all.each_with_index do |state, i|
                puts "#{i+1}. #{state.name}"
            end
```
14. For option 2, print/arrange the states from most to least confirmed cases.

```ruby
        when "2"
            states = State.all
            states.sort {|a,b| a.confirmed_cases <=> b.confirmed_cases}
            states[1..10].each_with_index do |state, i| 
              puts "#{i+1}. #{state.name} confirmed cases: #{state.confirmed_cases}"
            end
        end
```
- demo the CLI

**Note: Feel free to finish the rest of the options**

### Challenges for students
- Add a sign up functionality to the CLI
- Finish the rest of the options
#### References