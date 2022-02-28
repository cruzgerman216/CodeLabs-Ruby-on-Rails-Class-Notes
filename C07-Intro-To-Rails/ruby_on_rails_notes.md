# Ruby on Rails Basics

---
### Table of Contents 
- <a href="#scraping-state-statistics">Scraping State Statistics</a>
- <a href="#creating-the-grabable-module">Creating The Grabable Module</a>
- <a href="#save-method-refactoring">Save Method Refactoring</a>
- <a href="#cli-scraping-setup">CLI/Scraping Setup</a>
- <a href="#adding-logic-to-choose-option">Adding Logic To choose_option</a>
--- 


## What is Ruby on Rails?
Ruby on Rails ('Rails' for short), is a web application framework written in Ruby. Rails is used to create websites or APIs using the general-purpose programming language Ruby.

## Topics covered
- **MVC**
  - The Model-View-Controller (MVC) is an architectural pattern that separates an application into three main logical components: the model, the view, and the controller. Each of these components are built to handle specific development aspects of an application. MVC is one of the most frequently used industry-standard web development framework to create scalable and extensible projects.
    - **Model**
      - The Model component corresponds to all the data-related logic that the user works with. This can represent either the data that is being transferred between the View and Controller components or any other business logic-related data. For example, a Customer object will retrieve the customer information from the database, manipulate it and update it data back to the database or use it to render data.
    - **View**
      - The View component is used for all the UI logic of the application. For example, the Customer view will include all the UI components such as text boxes, dropdowns, etc. that the final user interacts with.
    - **Controller**
      - Controllers act as an interface between Model and View components to process all the business logic and incoming requests, manipulate data using the Model component and interact with the Views to render the final output. For example, the Customer controller will handle all the interactions and inputs from the Customer View and update the database using the Customer Model. The same controller will be used to view the Customer data.
  - **File/directories layout/structure**
    - **ERB**
      - Ruby embbeded code in an HTML file
    - **routes.rb**
      - A file that transpires a list of routes the user can request for
    - **stylesheets**
      - All css stylesheets are inserted in this directory
    - **Channel**
      - Channels acts as controllers for WebSocket requests by encapsulating the logic about particular works of unit, such as chat messages or notifications
    - **Helper**
      - Helper methods for the view
    - **Javascript**
      - Allows JavaScript to be executed throughout your application
    - **Controllers**
      - Managing requests from/to the view and managing the model logic of the application.
    - **Model**
      - Handling logic/structure of the data flow of your application
    - **Views**
      - Where your views get served in a full stack RoR application
    - **Config**
      - Configuration of files
    - **credentials.yml.enc**
      - hiding keys
    - **db**
      - Database in sqlite3 (can revert to posgresql which heroku prefers)
    - **gemfile**
      - similiar to package.json in a Javascript project, gems are a list of dependencies for development.
        - run ```bundle``` when adding new gems to the gemfile
  - **Rails Database**
    - SQL database that aligns data flow with tables
  - **CRUD**
    - Create, Read, Update, Delete
  - **ORM**
    - Object Relational Mapping - Rails uses ORM to translate ruby into sequel queries which makes it powerful and easy. 
  - **Active Record**
    - Active Record is the M in MVC - the model - which is the layer of the system responsible for representing business data and logic. Active Record facilitates the creation and use of business objects whose data requires persistent storage to a database. It is an implementation of the Active Record pattern which itself is a description of an Object Relational Mapping system.
  - **Resource**
    - table
    - model
    - controller
    - view
### Creating a basic Rails App

#### Generating a new Rails app

1. In an Code Editor (such as VS code), in the terminal, generate a new rails app 

```rails new my-app```
  - when adding new gems to your gem file, always run ```bundle``` in your terminal

2. Create a local server to build the rails application

```rails s```

3. Go to localhost:3000 and there should be no errors

#### Creating a Controller

4. Manually create a controller called books_controller.rb
  - define a class called BooksController that inherits properties/methods from the ApplicationController class

```ruby
class BooksController < ApplicationController
    
end
```

5. Define an instance method called index. 

```ruby
class BooksController < ApplicationController
    def index
        
    end
end
```

#### Creating a view/erb
6. Under views, create a folder called books, create a file called index.html.erb

7. In library.html.erb, create an h1 element with content ```index```

**NOTE: We don't need anything in the index method defined in the BooksController, rails is smart enough to locate the corresponding names of the method and the erb file names.**

8. Run rails and enter ```localhost:3000/books```

#### Routes
9. In the routes.rb file, create a route path corresponding to the index method.
```ruby 
Rails.application.routes.draw do
  get 'books', to:'books#index'
end
```

10. In the application controller, define a method called root and redirect to ```/books```
```ruby
    def root 
       redirect_to '/books'
    end
```

11. Go back to the routes.rb file, create a root path and use the root method.

```ruby
Rails.application.routes.draw do
  root 'books#root'
  get 'books', to:'books#index'
end
```
**EXPLANATION: Every time the application starts at localhost:3000, the root path will redirect to /books. Also, we can use the root method from the books controller due to inheriting methods from the application controller**

12.  Run ```rails s``` to test out the routes


#### Create A Resource

There's a much more easier way to create a view, model and controller. 

**DELETE/REMOVE all content created**
  - books conroller, view and routes as well as the root method in the application folder

13. Use the scaffold command to generate a book resource

```rails generate scaffold Book title:string author:string description:text```

- go over the generate files and code
  - books_controller.rb methods
    - <em>index</em> Book.all accesses all instances of Book from the database
    - <em>show</em> gets access to a partcilar book
    - <em>new</em> will create an instance of Book but will not be saved in the database
    - <em>edit</em>will create a view to edit a book
    - <em>create</em> create a book instance
    - <em>update</em> update a book instance
    - <em>destroy</em> will delete the book instance
  - views/books
    - <em>_form.html.erb</em> attains a form of fields that correspond to each Book Attribute that exists in the schema
    - <em>edit.html.erb</em> renders _form.html.erb and has options to go back or show the particular book
    - <em>index.html.erb</em> renders all book instances onto the page
    - <em>new.html.erb</em> renders a form to create a new book
    - <em>show</em> shows a particular book instance
  - db/migrate
    - migration files that will update the schema of the database

14. Run ```rails db:migrate``` in the console
    - the schema will be updated every time you run rails db:migrate. If you like to go back on an update form a migration, you can always run ```rails db:rollback```

15. enter ```rails routes --expanded```
    - scroll all the way to top to see the routes of Books. Notice the RESTful architecture rails creates with resources.

**NOTE: To show routes in a much more clearer way, you can type in a random path that doesn't exist and rails will show you the specific routes that exist in the application.**

16. Go through the application ```rails s```

#### References
- https://github.com/ruby/ruby
- https://www.tutorialspoint.com/mvc_framework/mvc_framework_introduction.htm
- https://www.pluralsight.com/guides/creating-a-chat-using-rails-action-cable
- https://guides.rubyonrails.org/active_record_basics.html