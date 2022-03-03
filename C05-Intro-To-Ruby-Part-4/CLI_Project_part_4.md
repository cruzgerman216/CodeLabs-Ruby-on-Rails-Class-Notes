# USA Covid CLI Tracker Part 4

---

### Table of Contents

- <a href="#seeding-users">Seeding Users</a>
- <a href="#Adding Bcrypt">Adding Bcrypt</a>
- <a href="#preparing-the-crud-module">Preparing The CRUD Module</a>
- <a href="#adding-user-login-to-cli">Adding User Login to CLI</a>

---

We will implement a login system to our CLI. Because we aren't able to store salted password in a database, we will fake this and create pre-defined values to log in. This code will be a good test run to work with Bcrypt.

<div id="seeding-users"></div>

### Seeding users

At the start of the application, we will have a list of user instances created with pre-defined usernames and passwords. To do this, create a file called `user.rb` and let's define a class called `User`

```ruby
    class User
    end
```

We want to ultimately store each user initialization in an array to be accessible.

```ruby
    @@users = []
```

Define a class method called `seed`, return an array of hashes with key-value pairs username and password. This is going to act as our "database".

```ruby
    def self.seed
        [{username: "test", password:"password"},
        {username: "johndoe123", password: "password1"}]
    end
```

Create attribute accessors `username` and `password`. Let's also define initialize and accept parameters corresponding to username and password.

```ruby
    attr_accessor :username, :password
    def initialize(username, password)
        @username = username
        @password = password
        @@users << self
    end
```

Upon each initialization, we like to store each instance and push it to the `@@users` array.

```ruby
        @username = username
        @password = password
        @@users << self
    end
```

In order to make the `@@users` variable accessible outside of the class, let's define a method called `all` that will do just that.

```ruby
    def self.all
        @@users
    end
```

<div id="adding-bcrypt"></div>

## Adding Bcrypt

Adding Bcrypt is very simple, navigate to the gemfile and add `gem 'bcrypt'` to the gemfile. 

```ruby
gem 'nokogiri', '~> 1.12', '>= 1.12.5'
gem 'open-uri', '~> 0.1.0'
gem 'bcrypt'
```

Run `bundle install` in your terminal.

<div id="preparing-the-crud-module"></div>

## Preparing the CRUD module

Create a file called `crud.rb` under the lib directory. Define a module called `CRUD`. 

```ruby
module CRUD
end
```

Use `self` to define a method called `create_hash_digest` that has one parameter. Use `BCrypt::Pasword.create()` to create a hash out of the password. This allows us to return a hash.

```ruby
module CRUD
    def self.create_hash_digest(password)
        BCrypt::Password.create(password)
    end
end
```

Let's create a module method that allows you to salt each user's password. Use the built in method `each` to iterate through each user.

```ruby
  def self.create_secure_users
    User.seed.each do |user_record|
      User.new(user_record[:username], create_hash_digest(user_record[:password]))
    end
  end
```

The next logic we will have to create is the ability to authenticate a user. In the CLI, we will ask the user for his/her username and password, when that happens, we will call `authenticate_user`. 

```ruby
  def self.authenticate_user(username, password)
  end
```

Initialize a variable `currUser` to nil. If the username and password match, then we will return the specific user. Iterate through the list of intialized `users`.
```ruby 
    currUser = nil
    User.all.each do |user|
    end
    currUser
```

We can simply check each username and password to find  a match. If so, stop the iteration and return the specific user whose matches password and username.

```ruby
    currUser = nil
    User.all.each do |user|
      if user.username == username && user.password == password
        currUser = user
        break;
      end
    end
    currUser
```

Load `crud.rb`, `bcrypt` and `user.rb` in the USA_Covid_19_Tracker.rb file

```ruby
require "bcrypt"
require_relative "country.rb"
require_relative "state.rb"
require_relative "scraper.rb"
require_relative "user.rb"
require_relative "crud.rb"
require_relative "USA_Covid_19_Tracker/cli.rb"
```

<div id ="adding-user-login-to-cli"></div>

## Adding user login to CLI

In `cli.rb`, under the `run` method, call `create_secure_users` from the CRUD module. At the start of our application, we want to secure each user's password. As a coder, we understand our username and password are exposed from the code, of course, this is a good testing run to use Bcrypt.

```ruby
    def run
        CRUD.create_secure_users
        system('clear')
        greeting
```

Now, create a `login` method. Declare a variable called `authenticated` and set it to false.

```ruby
    def login
        authenticated = false
    end
```

We will use `authenticated` to keep track of the condition of the `while` loop. If the user logs in correctly, we exit the loop. If not, the user will be asked again for the correct login. Create a `while` loop and ask the user for username and password.

```ruby
        while authenticated != true
            puts "Please login"
            puts "> What is your username?"
            username = gets.chomp.downcase
            puts "> What is your password?"
            password = gets.chomp.downcase
        end
```

Use the CRUD method `authenticated_user` to authenticate the user. If the user `exists`, set authenticated to true. Otherwise, prompt the user to try again.

```ruby
            puts "Please login"
            puts "> What is your username?"
            username = gets.chomp.downcase
            puts "> What is your password?"
            password = gets.chomp.downcase
            if CRUD.authenticate_user(username, password)
                authenticated = true
            else
            "Please try again"
            end
```

Include the login method in the run method

```ruby
        CRUD.create_secure_users
        greeting
        login
```

Result:

```
    > Starting program!
    > What is your username?
    > wrong usernmae
    > What is your password
    > wrong password
    > Please try again
    > What is your username?
    > test
    > What is your password
    > password
    > 1. Option 1
    > 2. Option 2
    > 3. Option 3
    > What would you like to choose?
```

--- 
- [Next Part](https://github.com/cruzgerman216/CodeLabs-Ruby-on-Rails-Class-Notes/blob/main/C03-Intro-To-Ruby-Part-2/CLI_Project_part_5.md)

---

Saw a mispelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 