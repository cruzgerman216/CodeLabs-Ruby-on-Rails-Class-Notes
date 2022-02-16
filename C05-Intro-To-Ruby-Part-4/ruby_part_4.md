# The Ruby Programming Language

## Topics covered
- **Authenticatation systems**
  - Passwords are vulnerable to hackers. Passwords are never stored as just strings in the database. There are tools that allow you to encrypt passwords to prevent security vulnerabilities. An example of a tool is Bcrpyt.
- **Bcrpyt**
  - bcrypt() is a sophisticated and secure hash algorithm designed by The OpenBSD project for hashing passwords. The bcrypt Ruby gem provides a simple wrapper for safely handling passwords.
- **Modules**
  - In Ruby, modules are somewhat similar to classes: they are things that hold methods, just like classes do. However, modules can not be instantiated. I.e., it is not possible to create objects from a module. And modules, unlike classes, therefore do not have a method new .
## USA Covid CLI Tracker Part 4

### Adding BCrypt + user login functionality

1. add bcrpt to your gemfile

```ruby
gem 'nokogiri', '~> 1.12', '>= 1.12.5'
gem 'open-uri', '~> 0.1.0'
gem 'bcrypt'
```
2. run 'bundle install' in your terminal
3. create a file called crud.rb under the lib directory
4. Define a module called CRUD. At the top of the file, require 'bcrypt'
```ruby
require 'bcrypt'
module CRUD

end
```
5. Define a class method called create_hash_digest that has one parameter
6. Use bcrpyt to salt the password

```ruby
require 'bcrypt'
module CRUD
    def self.create_hash_digest(password)
        BCrypt::Password.create(password)
    end
end
```
7. define a class method that allows you to verify a password using bcrypt

```ruby 
    def self.verify_hash_digest(password)
        BCrypt::Password.new(password)
    end
```

8. Define a method that allows you to salt user passwords. Use the built in method ```each``` to iterate through each users. This will allow you to salt each user password when this method gets called.

```ruby
    def self.create_secure_users(users)
       users.each do |user_record|
            user_record[:password] = create_hash_digest(user_record[:password])
        end
    end
```

9. Define a method that will authenticate a user.

```ruby
    def self.authenticate_user(username, password, users)
        users.each do |user_record|
            if user_record[:username] == username && verify_hash_digest(user_record[:password]) == password 
                return user_record
            end
        end
        puts "Crendtials not correct"
    end
```

10. final result

```ruby
require 'bcrypt'
module CRUD
    def self.create_hash_digest(password)
        BCrypt::Password.create(password)
    end

    def self.verify_hash_digest(password)
        BCrypt::Password.new(password)
    end

    def self.create_secure_users(users)
        users.each do |user_record|
            user_record[:password] = create_hash_digest(user_record[:password])
        end
    end

    def self.authenticate_user(username, password, users)
        users.each do |user_record|
            if user_record[:username] == username && verify_hash_digest(user_record[:password]) == password 
                return user_record
            end
        end
        puts "Crendtials not correct"
    end
end
```
#### User class

11. Define a class called User

```ruby
    class User

    end
```

12. Define a class variable called users. Set it to an array of objects with key pair values username and password.
```ruby
    @@users = [
        {username: "test", password:"password"}
    ]
```
13. Create attribute accesors username and password. Define an iniitalize method that allows you to store these values.

```ruby
    attr_accessor :username, :password
    def initialize(username, passsord)
        @username = username
        @password = password
    end
```
14. Define a class method called all that returns the users attribute.

```ruby
    def self.all 
        return @@users
    end
```

15. Final result

```ruby
class User 
    @@users = [
        {username: "test", password:"password"}
    ]

    attr_accessor :username, :password
    def initialize(username, passsord)
        @username = username
        @password = password
    end
    def self.all 
        return @@users
    end
end
```

16. 'import' crud.rb and users.rb in the USA_Covid_19_Tracker.rb file

```ruby
require_relative "USA_Covid_19_Tracker/cli.rb"
require_relative "./country.rb"
require_relative "./state.rb"
require_relative "./scraper.rb"
require_relative "./crud.rb"
require_relative "./users.rb"
```

### Adding user login to CLI

17. In cli.rb, under the run method, call create_secure_users from the CRUD module.
    - At the start of our application, we want to secure each user's password. Of course, this is a good testing run to use bcrypt. We will use bcypt while we learn Rails later in the course.

```ruby
    def run
        system('clear')
        CRUD.create_secure_users(User.all)
        greeting
```

18. Create a login method. 

- define a variabled called authenticated and set it to false
    - we will use authenticated to keep track of a while loop. If the user logs in correctly, we exit the loop. If not, the user will be ask again for the correct login.
```ruby 
    def login
        authenticated = false
    end
```

19. Create a while loop and ask the user for username and password.

```ruby
        while authenticated != true
            puts "Please login"
            puts "What is your username?"
            username = gets.chomp.downcase
            puts "What is your password?"
            password = gets.chomp.downcase
        end
```


19. Use the CRUD method authenticated_user to authenticate the user 

```ruby 
            puts "Please login"
            puts "What is your username?"
            username = gets.chomp.downcase
            puts "What is your password?"
            password = gets.chomp.downcase
            if CRUD.authenticate_user(username, password, User.all)
                authenticated = true
            end
```

20. Include the login method in the run method

```ruby
        CRUD.create_secure_users(User.all)
        greeting
        login
```
#### References
- https://github.com/bcrypt-ruby/bcrypt-ruby
- https://rubygems.org/gems/bcrypt/versions/3.1.12#:~:text=bcrypt()%20is%20a%20sophisticated,wrapper%20for%20safely%20handling%20passwords.
- http://ruby-for-beginners.rubymonstas.org/advanced/modules.html#:~:text=In%20Ruby%2C%20modules%20are%20somewhat,not%20have%20a%20method%20new%20.