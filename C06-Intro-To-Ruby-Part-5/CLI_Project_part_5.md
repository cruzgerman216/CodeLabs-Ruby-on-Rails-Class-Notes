# USA Covid CLI Tracker Part 5

---
### Table of Contents 
- <a href="#scraping-state-statistics">Scraping State Statistics</a>
- <a href="#creating-the-grabable-module">Creating The Grabable Module</a>
- <a href="#save-method-refactoring">Save Method Refactoring</a>
- <a href="#cli-scraping-setup">CLI/Scraping Setup</a>
- <a href="#adding-logic-to-choose-option">Adding Logic To choose_option</a>
--- 

<div id="scraping-state-statistics"></div>

## Scraping state statistics

In the scraper.rb file, inside of the class method `scrape_states`, scrape all 50 states from the provided URL. Inspect the elements from the page we are scraping and indicate the specific elements we want to scrape data from. Inside the scrape_usa method, print to the console "Scraping USA stats". (When we later call this scrape method, we will let the user know that scraping data is currently undergoing)
```ruby
    def self.scrape_states
        puts "Scraping States stats"
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        states_table = doc.css('tbody tr')
    end
```

Iterate through from 1 to 51 of the array (skipping 0 due to it being the column headers). And get the content from each element. Be Sure to also include the `Printable::Styles` module
```ruby

        states_table[1..51].each do |row|
            name = row.css('td')[1].text.split(" ").join(" ")
            cases = text_to_integer( row.css('td')[2].text)
            deaths = text_to_integer(row.css('td')[4].text)
            recoveries = text_to_integer(row.css('td')[6].text)
        end
```

Create a state instance when iterating through the states_table array and store pass in the corresponding values. Check to see if the name equals "Distrct Of Columbia". If so, skip it (DoC is part of the table and isn't a state).
**EXPLANATION: We want to ultimately save the instances in the State class so that our other classes have access to it.**
```ruby
            deaths = text_to_integer(row.css('td')[4].text)
            recoveries = text_to_integer(row.css('td')[6].text)
            if name != "District Of Columbia"
                State.new(name: name, overall_deaths:deaths, confirmed_cases: cases, recoveries:recoveries)
            end
```

<div id="creating-the-grabable-module"></div>

## Creating the Grabable module
Let's create another module called `Grabable`. Let's create a save method that allows instances to be "saved" under their class. Create a grabable.rb file under the concerns directory. In the file, define a module called Grabable. Inside `Grabable`, define another module called `Store`.

```ruby
module Grabable
    module Store
    end
end
```

In the `Store` module, define a method called `save` that will push the class instance to `all`. 

```ruby
        def save 
            self.class.all << self 
        end
```

<div id="save-method-refactoring"></div>

## Save Method Refactoring

Navigate to 'lib/covid_19.rb' and load `grabable.rb`. It's important to include it at the top because if other files depend on it, it will have to load first.

```
require_relative "grabable.rb"
require_relative "crud.rb"
require_relative "scraper.rb"
```

In the country.rb file, include the `Store` module in the Country class

```ruby 
    class Country
    include Grabable::Store
```

In the initialize method, include the save method.
```ruby
    def initialize(attributes)
        attributes.each {|key, value| self.send("#{key}=", value)}
        save
    end
```
In the state.rb file, define an intialize method and include the super keyword

```ruby
    @@all = []
    def initialize(attributes)
        super
    end
```

<div id="cli-scraping-setup"></div>

## CLI/Scraping setup
In the cli.rb, define a method called `scrape`. Call `scrape_usa` and `scrape_states` from the Scrapper class
```ruby
    def scrape
        Scrapper.scrape_usa
        Scrapper.scrape_states
    end
```

Call the `scrape` method at the top of the run method

```ruby
    def run
        scrape
        system('clear')
        CRUD.create_secure_users(User.all)
```

## CLI Menu
In the menu, let's allow the user to see the top ten states with the most confirmed cases and least most confirmed cases.

```ruby
  def menu
    puts "1. Top 10 States with the most confirmed cases"
    puts "2. Top 10 States with the least confirmed cases"
    puts "What would you like to choose?"
    input = gets.chomp
    choose_options(input)
    input
  end
```

<div id="adding-logic-to-choose-option"></div>

## Adding logic to choose_option
In the `choose_option` menu, print out all the instances the State class holds in the @@all attribute for when users choose option 1 in the menu.

```ruby 
     when "1"
      states = State.all
      # sorting from least to greatest, calling the reverse method will reverse the array
      states.sort_by { |state| state.confirmed_cases }.reverse
      # Iterating through the first ten elements, printing each
      states[0..9].each_with_index do |state, i|
        puts "#{i + 1}. #{state.name} confirmed cases: #{state.confirmed_cases}"
      end
```
For option 2, print/arrange the states from most to least confirmed cases.

```ruby
    when "2"
      states = State.all
      # sorting from least to greatest
      states.sort_by { |state| state.confirmed_cases }
      # Iterating through the first ten elements, printing each
      states[0..9].each_with_index do |state, i|
        puts "#{i + 1}. #{state.name} confirmed cases: #{state.confirmed_cases}"
      end
```

Demoo the CLI

**Note: Feel free to finish the rest of the options**


## References