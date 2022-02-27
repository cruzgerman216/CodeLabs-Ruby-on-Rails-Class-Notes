# USA Covid CLI Tracker Part 4

We will implement a log in system to our CLI. Because we aren't able to store salted password in a database, we will fake this and create pre-defined values to log in. This code will be a good test run through to work with Bcrypt.

## Adding Bcrypt

Add Bcrypt to your gemfile

```ruby
gem 'nokogiri', '~> 1.12', '>= 1.12.5'
gem 'open-uri', '~> 0.1.0'
gem 'bcrypt'
```

Run `bundle install` in your terminal.

## Preparing the CRUD module

Create a file called crud.rb under the lib directory. Define a module called CRUD. At the top of the file, require 'bcrypt' such as

```ruby
require 'bcrypt'
module CRUD
end
```

Use `self` to define a method called `create_hash_digest` that has one parameter. Use `BCrypt::Pasword.create()` to create a hash out of the password. This allows us to return a hash.

```ruby
require 'bcrypt'
module CRUD
    def self.create_hash_digest(password)
        BCrypt::Password.create(password)
    end
end
```

Let's create a method that allows you to salt each user's password. Use the built in method `each` to iterate through each user.

```ruby
  def self.create_secure_users
    User.seed.each do |user_record|
      User.new(user_record[:username], create_hash_digest(user_record[:password]))
    end
  end
```

Then,let's use `self` to define a method that will authenticate a user.

```ruby
  def self.authenticate_user(username, password)
    currUser = nil
    User.all.each do |user|
      if user.username == username && user.password == password
        currUser = user
        break;
      end
    end
    currUser
  end
```

### Seeding users

At the start of the application, we will have a list of user instances created with pre-defined usernames and passwords. To do this, create a file called `user.rb` and let's define a class called `User`

```ruby
    class User
    end
```

Define a class variable called users.

```ruby
    @@users = []
```

Define a class method called `seed`, return an array of hashes with key-value pairs username and password. The reason why

```ruby
    def self.seed
        [{username: "test", password:"password"},
        {username: "johndoe123", password: "password1"}]
    end
```

Create attribute accessors username and password. Define an initialize method that allows you to store these values.

```ruby
    attr_accessor :username, :password
    def initialize(username, password)
        @username = username
        @password = password
        @@users << self
    end
```

Define a class method called `all` that returns the users.

```ruby
    def self.all
        @@users
    end
```

Load crud.rb and user.rb in the USA_Covid_19_Tracker.rb file

```ruby
require_relative "USA_Covid_19_Tracker/cli.rb"
require_relative "country.rb"
require_relative "state.rb"
require_relative "scraper.rb"
require_relative "crud.rb"
require_relative "user.rb"
```

## Adding user login to CLI

In cli.rb, under the `run` method, call `create_secure_users` from the CRUD module. At the start of our application, we want to secure each user's password. As a coder, we understand our username and password are exposed from the code, of course, this is a good testing run to use Bcrypt. We will use Bcypt while we learn Rails later in the course.

```ruby
    def run
        system('clear')
        CRUD.create_secure_users
        greeting
```

Now, create a `login` method. Declare a variable called `authenticated` and set it to false.

```ruby
    def login
        authenticated = false
    end
```

We will use `authenticated` to keep track of the condition of the while loop. If the user logs in correctly, we exit the loop. If not, the user will be asked again for the correct login. Create a while loop and ask the user for username and password.

```ruby
        while authenticated != true
            puts "Please login"
            puts "What is your username?"
            username = gets.chomp.downcase
            puts "What is your password?"
            password = gets.chomp.downcase
        end
```

Use the CRUD method `authenticated_user` to authenticate the user

```ruby
            puts "Please login"
            puts "What is your username?"
            username = gets.chomp.downcase
            puts "What is your password?"
            password = gets.chomp.downcase
            if CRUD.authenticate_user(username, password)
                authenticated = true
            end
```

Include the login method in the run method

```ruby
        CRUD.create_secure_users
        greeting
        login
```
