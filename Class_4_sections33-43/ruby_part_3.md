# The Ruby Programming Language

## Topics covered
- [style guide](https://github.com/rubocop/ruby-style-guide) - conventional naming and style for ruby programming 
  - variables and methods are snake case
  -  class names - each word is capitalized
- Object Oriented Programming (OOP) is a programming paradigm that used objects and their interactions to design and program application.
- class
  - A class is a blueprint from which objects are created. 
- Nokogiri  
  - Nokogiri is an open source software library to parse HTML and XML in Ruby. 
- Open-uri
  - OpenURI is an easy-to-use wrapper for Net::HTTP, Net::HTTPS and Net::FTP.

## USA Covid CLI Tracker Part 3

### Scrapping Setup

1. create a file called scraper.rb under the lib directory. Include the file in the USA_Covid_19_Tracker.rb file

```ruby
require_relative "USA_Covid_19_Tracker/cli.rb"
require_relative "./country.rb"
require_relative "./state.rb"
require_relative "./scraper.rb"
```
2. define a class called Scrapper and include two class methods scrape_usa and scrape_states


```ruby
class Scrapper

    def self.scrape_usa
    end

    def self.scrape_states
    end
end

```

3. Create a attribute and set it to the url we will be scraping from

```ruby
    URL = "https://www.worldometers.info/coronavirus/country/us/"
```

4. In the gemfile, add gems nokogiri and open-uri

```ruby
    git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

    # Specify your gem's dependencies in USA_Covid_19_Tracker.gemspec
    gemspec

    gem 'nokogiri', '~> 1.12', '>= 1.12.5'
    gem 'open-uri', '~> 0.1.0'
```
5. run ```bundle install```

You may get an error from your gemspec file
```The gemspec at /Users/username/Documents/GitHub/Ruby-GEM-CLI-Covid-Tracker/USA_Covid_19_Tracker/USA_Covid_19_Tracker.gemspec is not
valid. Please fix this gemspec.
The validation error was 'metadata['homepage_uri'] has invalid link: "TODO: Put your gem's website or public repo URL here."```

- comment out this section in your gemspec file 

```ruby
  # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
  # to allow pushing to a single host or delete this section to allow pushing to any host.
 
  # if spec.respond_to?(:metadata)
  #   spec.metadata["allowed_push_host"] = "TODO: Set to 'http://mygemserver.com'"

  #   spec.metadata["homepage_uri"] = spec.homepage
  #   spec.metadata["source_code_uri"] = "TODO: Put your gem's public repo URL here."
  #   spec.metadata["changelog_uri"] = "TODO: Put your gem's CHANGELOG.md URL here."
  # else
  #   raise "RubyGems 2.0 or newer is required to protect against " \
  #     "public gem pushes."
  # end
```

set these attributes to an empty string
```ruby
 spec.summary       = ""
  spec.description   = ""
  spec.homepage      =  ""
  spec.license       = "MIT"
```
You may also get a bundler issue 
 change the version to what you have, I have version 2.2.32

 ```ruby
spec.add_development_dependency "bundler", "~> 2.2.32"
  spec.add_development_dependency "rake", "~> 10.0"
  ```

run bundle install to install the necessary gems


6. Parse html using Nokogiri and open-uri

```ruby
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        puts doc
    end
```

7. test this out and call this method 

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

8. In the scrape_usa method, use the css method to target specific classes of the document. This should return an array of text. Use the dev tools to inspect the document of what specific elements you are interested in. In this case, usa main data. This might be tricky to locate the css classes.

```ruby 
    def self.scrape_usa
        doc = Nokogiri::HTML(URI.open("https://www.worldometers.info/coronavirus/country/us/"))
        country_main = doc.css(".content-inner .maincounter-number")
```
keep printing each value as we add things little by little.

9. Store the first element. 

```ruby 
usa_confirmed_cases = country_main[0]
```

10. Use the text method to get the content of the element 

```ruby 
usa_confirmed_cases = country_main[0].text

```

11.  Use regex to get rid of every whitespace and comma. Then turn it into an interger.

```ruby
        usa_confirmed_cases = country_main[0].text.gsub(/[\s,]/,"").to_i
```

12. Do this repeatedly for the confirmed deaths and recoveries.

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

13. Create a ```concerns``` folder. Create a file called ```printable.rb```. In that, define a module called printable and in that module, define another module called styles.

```ruby
module Printable
    module Styles
    end
end
```

14. Define a method called text_to_integer. Use the logic we used in the scrape_usa data to do this.

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

15. Refactor the scraper class. Extend the printable module 

```ruby
class Scrapper
    extend Printable::Styles
```

16. Refactor the scrape_usa data
```ruby
        usa_confirmed_cases = text_to_integer(country_main[0].text)
        usa_overall_deaths = text_to_integer(country_main[1].text)
        usa_recoveries = text_to_integer(country_main[2].text)
    end
```

17. Initialize a Country instance with this data
Don't forget to require_relative the Country class
```ruby
        usa_recoveries = text_to_integer(country_main[2].text)
        usa = Country.new(name: "USA", confirmed_cases: usa_confirmed_cases, overall_deaths:usa_overall_deaths, recoveries:usa_recoveries)
    end
```


#### References
- https://en.wikipedia.org/wiki/Nokogiri_(software)#:~:text=Nokogiri%20is%20an%20open%20source,from%20the%20rubygems.org%20repository.
- https://ruby-doc.org/stdlib-2.6.3/libdoc/open-uri/rdoc/OpenURI.html