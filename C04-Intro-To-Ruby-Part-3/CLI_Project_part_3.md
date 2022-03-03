# USA Covid CLI Tracker Part 3
<img src="https://miro.medium.com/max/636/0*08Uq7LNwpSfEA3zc.jpeg"/>

In Part 3, we will will focus primarily on setting up classes `Country` and `State`. We will also add gems `Nokogiri` and `open-uri`. These gems will be used to scrape info from a site and parse the HTML document. We ultimately want to print the info we scrape onto the console. Let's explore the webpage we like to scrape.

---
### Table of Contents 
- <a href="#Site-Covid-19-Statistics">Site with Covid-19-Statistics</a>
- <a href="#Preparing-the-Country-Class">Preparing the Country Class</a>
- <a href="#Basic-Meta-Programming-using-send">Basic Meta Programming using send</a>
- <a href="#Preparing-the-State-Class">Preparing the State Class</a>
- <a href="#Loading-Country-and-State">Loading Country and State</a>
- <a href="#Setting-up-Open-URI-and-Nokogiri-gems">Setting up Open-URI and Nokogiri gems</a>
- <a href="#Data-Scraping">Data Scraping</a>
- <a href="#Regex">Regex</a>
- <a href="Printable-Module">Printable Module</a>
--- 

<div id="Site-Covid-19-Statistics"></div>

## Site with Covid-19-Statistics
We will use this [site](https://www.worldometers.info/coronavirus/country/us/) to scrape data from. The way we will organize our data is to separate `countries` and `states`. We will use classes to do this.

## Preparing the Country Class
We will go ahead and use Ruby Classes to structure the Covid-19 data for countries and states. Create a file under lib called `country.rb`. Define a class called `Country`. 

```ruby
class Country 
end
```
Let's include attribute accessors for name, confirmed_cases, overall_deaths, and recoveries. 

```ruby 
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
end
```
To store Country Instances, declare a class variable and set it to an empty array.

```ruby 
@@Countries = []
```
Create a class method all that returns the @@Countries property.

```ruby
class Country
    attr_accessor :name, :confirmed_cases, :overall_deaths, :recoveries
    @@Countries = []

    def self.all
        @@Countries
    end
end
```

Upon instantiation, we like to set the country attributes. To do this, define the intialize method. 

```ruby
    def initialize()
    end
```

Let's add parameters to `initialize` to set each instance variable upon instantiation. 

```ruby
    def initialize(name, confirmed_cases, overall_deaths, recoveries)
        @name = name
        @confirmed_cases = confirmed_cases
        @overall_deaths = overall_deaths
        @recoveries = recoveries
    end
```

<div id="Basic-Meta-Programming-using-send"></div>

## Basic Meta Programming using send

Assigning values to each instance variable is redundant. Lets incorporate some meta programming. Meta programming can be referred to as "code that writes code for us". Include only one parameter called attributes. Get rid of the code lines within the method definition aswell.

```ruby
    # Attributes will be a hash
    def initialize(attributes)

    end
```
Let's use the hash method `each` to iterate through the hash. Include a one line code block `{}`. By using the method each, we can reference each key and it's value by coding the following: 

```ruby
    # Attributes will be a hash
    def initialize(attributes)
        attributes.each {|key, value| }
    end
```

In the one line statement, use `self` to invoke the `send` method. The send method will take in two arguments. The first will be the name of a method we like to execute. In this case, we like to execute a setter method for each attribute.

```ruby
    # Attributes will be a hash
    def initialize(attributes)
        attributes.each {|key, value| self.send("#{key}=") }
    end
```

Remember `attr_accessor` provides these methods for each instance variable. For example, here is the setter for the inatance variable `name`:

```ruby 
    def name=(value)
        @name = value;
    end
```

Notice how this method definition also includes a parameter. We can pass in the value as a second argument in `send`.
```ruby 
    def initialize(attributes)
    # Example:
    # new Country({:name=>"USA"})
        attributes.each {|key, value| self.send("#{key}=", value)}
    end
```

<div id="Preparing-the-State-Class"></div>

## Preparing the State Class
After defining the Country class, let's create a file called `state.rb` under lib and define the `State` class. Let's allow the `State` class to be the subclass/inherit from Country.

```ruby
class State < Country
    @@states = []
    def self.all
        @@states
    end
end
```

<div id="Loading-Country-and-State"></div>

## Loading Country and State
Include country.rb and state.rb in USA_Covid_19_Tracker.rb
```ruby
    require_relative "USA_Covid_19_Tracker/cli.rb"
    require_relative "./country.rb"
    require_relative "./state.rb"
```

This would be a good time to check if things work! :thumbsup:

<div id="Scraping-Setup"></div>

## Scraping Setup
Create a file called scraper.rb under the lib directory. Include the file in the USA_Covid_19_Tracker.rb file

```ruby
require_relative "USA_Covid_19_Tracker/cli.rb"
require_relative "country.rb"
require_relative "state.rb"
require_relative "scraper.rb"
```

Define a class called Scrapper and include two class methods `scrape_usa` and `scrape_states`.


```ruby
class Scrapper

    def self.scrape_usa
    end

    def self.scrape_states
    end
end

```

Create a class constant and set it to the url we will be scraping from. A class constant cannot be reassigned.

```ruby
class Scrapper
    URL = "https://www.worldometers.info/coronavirus/country/us/" # class constant

    def self.scrape_usa
    end
```

<div id="Setting-up-Open-URI-and-Nokogiri-gems"></div>

## Setting up Open-URI and Nokogiri gems
In the gemfile, add gems Open-URI and Nokogiri. Open-URI allows you to make HTTP requests that will ultimately return a document of the webpage. Nokogiri will allow us to parse HTML in Ruby. 

```ruby
    git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

    # Specify your gem's dependencies in USA_Covid_19_Tracker.gemspec
    gemspec

    gem 'nokogiri', '~> 1.12', '>= 1.12.5'
    gem 'open-uri', '~> 0.1.0'
```
Run ```bundle install``` 
(If you are in Replit, be sure to run this in the root directory. If you are in VS Code, run this in the project folder)

<div id="Bundling-Issue"></div>

## Bundling Issue
You may get an error from your gemspec file

```The gemspec at /Users/username/Documents/GitHub/Ruby-GEM-CLI-Covid-Tracker/USA_Covid_19_Tracker/USA_Covid_19_Tracker.gemspec is not
valid. Please fix this gemspec.
The validation error was 'metadata['homepage_uri'] has invalid link: "TODO: Put your gem's website or public repo URL here."
```

Navigate to the Gemfile and comment out `gemspec`. We aren't interested at the moment to specify the dependencies of the program.


```ruby
# Specify your gem's dependencies in covid_19.gemspec
# gemspec
```

Run bundle install to install the necessary gems.

<div id="Data-Scraping"></div>

## Data Scraping
We must first require both `nokogiri` and `open-uri` at the top of the file. Then we can use the objects `Nokogiri` and `URI`. 


```ruby 
require 'nokogiri'
require "open-uri"
```

Let's start the process of Data Scraping. First we will call `URI.open()`. This method call will take in a url and create an entirely new file instance with contents of the webpage.

We can then use the `Nokogiri` and call `HTML`. The `HTML` takes in a document or a file that will parse the html and in return give us an object full of values and methods. We will pass in our file instance as an argument.

```ruby
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open(URL))
        puts doc
    end
```

Let's test this method out. 

```ruby
Scrapper.scrape_usa
```
run ```ruby lib/scraper.rb```

You should get something similiar to this. It should be a large document.
```
.
.
.
<div class="col-md-9" style="padding-left:0px;margin-left:0px;">
+ <input name="new_serious" type="number" onkeypress="return event.charCode &gt;= 48 &amp;&amp; event.charCode &lt;= 57" class="form-control case_number" id="new_serious">  =  <input onkeypress="return event.charCode &gt;= 48 &amp;&amp; event.charCode &lt;= 57" name="total_serious" type="number" class="form-control case_number" id="total_serious"> total serious  </div>
</div>
<div class="form-group row">
<div class="col-md-3">
<label for="total_tests" class="form_labels" style="line-height:34px;">Total Tests:</label>
</div>
<div class="col-md-9" style="padding-left:0px;margin-left:0px;">
<span style="color:#fff;">+</span> <input onkeypress="return event.charCode &gt;= 48 &amp;&amp; event.charCode &lt;= 57" name="total_tests" type="number" class="form-control case_number" id="total_tests">
.
.
.
```

In the `scrape_usa` method, use the css method to target specific classes of the document. This should return an array of text. Use the dev tools to inspect the document of what specific elements you are interested in. In this case, usa main data. This might be tricky to locate the css classes.

```ruby 
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        country_main = doc.css(".maincounter-number")
```

`doc.css("")` will return to us an array of elements. We see that by testing and reading the values, we see that `country_main[0]` gives us one desired element.


```ruby 
usa_confirmed_cases = country_main[0]
```
Use the text method to get the content of the element 

```ruby 
usa_confirmed_cases = country_main[0].text

```

<div id="regex"></div>

## Regex

We will use the built in string method `gsub` to get rid of any unecessary spacing and commas. 

```ruby
    usa_confirmed_cases = country_main[0].text.gsub(/[]/)
```

Within the square brackets, include `\s` in which references every white space in the string. After `\s` include a `,`. 
```ruby
    usa_confirmed_cases = country_main[0].text.gsub(/\s,/)
```
Pass in empty strings as the second argument. This is saying, "Get rid of every white space and comma in this string".

```ruby
        usa_confirmed_cases = country_main[0].text.gsub(/[\s,]/,"").to_i
```

Do this repeatedly for the confirmed deaths and recoveries.

```ruby
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        country_main = doc.css(".content-inner .maincounter-number")
        usa_confirmed_cases = country_main[0].text.gsub(/[\s,]/,"").to_i
        usa_overall_deaths = country_main[1].text.gsub(/[\s,]/,"").to_i
        usa_recoveries = country_main[2].text.gsub(/[\s,]/,"").to_i
    end
```
Let's refactor this.

<div id="Printable-Module"></div>

## Printable Module

Create a ```concerns``` folder. Create a file called ```printable.rb```. In that, define a module called printable and in that module, define another module called styles.

```ruby
module Printable
    module Styles
    end
end
```

Define a method called text_to_integer. Use the logic we used in the scrape_usa data to do this.

```ruby
module Printable
    module Styles
        def text_to_integer(text)
            text.gsub(/[\s,]/,"").to_i
        end
    end
end
```

There will be times where our value will be an empty string. In this case, we will return a 0.

```ruby
module Printable
    module Styles
        def text_to_integer(text)
            text == "" ? output = 0 : output = text.gsub(/[\s,]/,"").to_i
            output
        end
    end
end
```

Refactor the scraper class. Extend the printable module 

```ruby
class Scrapper
    extend Printable::Styles
```

Refactor the `scrape_usa` method. At the top of the file, include:
```ruby 
require_relative './concerns/printable.rb'
```
Refactor using the method we defined.

```ruby
        usa_confirmed_cases = text_to_integer(country_main[0].text)
        usa_overall_deaths = text_to_integer(country_main[1].text)
        usa_recoveries = text_to_integer(country_main[2].text)
    end
```

 Initialize a Country instance with this data
At the top of the file 
```ruby 
require_relative "./country.rb"
```
```ruby
        usa_recoveries = text_to_integer(country_main[2].text)
        usa = Country.new(name: "USA", confirmed_cases: usa_confirmed_cases, overall_deaths:usa_overall_deaths, recoveries:usa_recoveries)
    end
```

--- 
- [Next Part](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C05-Intro-To-Ruby-Part-4/CLI_Project_part_4.md)

---

Saw a mispelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 