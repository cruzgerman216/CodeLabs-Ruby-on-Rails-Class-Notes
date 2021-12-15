  # Ruby on Rails - CRUD Operations Part 1
  ## BookIt App Part 1

## Topics covered
- **<em>Resource naming conventions</em>** 
  - Resource example
    - Model name: article
    - Article model file name: article.rb
    - Article model class name: Article
    - Table name: articles
  - **<em>rails db:migrate</em>** - will update the schema file for any new migration files
  - **<em>rails db:rollback</em>** - to revert such mistakes we use db:rollback , which restores the database to a state, before the latest migration was run. 
  - **<em>rails c</em>** 
    - Once in the terminal, you can exit the console by entering ```exit```
    - rails console allows you to test your tables or models such as ```Article.all```
    - when updating the model, you can enter ```reload!``` to reflect those changes
  - **<em>Expand routes in terminal</em>**
    - ```Rails routes --expanded ```
  - **<em>params</em>** is a hash with key pair values of parameters being passed in from a request.
  - **<em>instance variables defined in controller methods</em>** are able to be read from the view
  - **<em>embedded ruby tabs</em>** ```<% %>``` allows instance variabled to be evaluated(used) in the view
    - displayed ruby code ```<%= %>```
  - **<em>byebug</em>**
    - Ruby gem used for testing
    - <em>The debugger permits the ability to understand what is going on inside a Ruby program while it executes </em>

### Adding CRUD functionality

#### Generating a new Rails app

1. In a Code Editor (such as VS code), in the terminal, generate a new rails app 

```rails new book-it```
  - Run ```rails s```  and go to "localhost:3000" to check if successfully created

#### Adding a migration
1. Let's create a migration file so we can create/update the schema file(The schema file is in charge of what the data looks like when saving/deleting/creating/reading). Enter the following 

```rails g migration create_books```

2. Navigate to db/migrate to locate the specified migration file you created
   - add a title attribute to the table. Make it of type string. Leave the timestamps atribute there.
```ruby
class CreateBooks < ActiveRecord::Migration[6.1]
  def change
    create_table :books do |t|
      t.string :title
      t.timestamps
    end
  end
end
```

3. enter ```rails db:migrate``` to create the books table
   - check the details of the table in the schema file
   - rails db:migrate will run any new migration files (running it again will not do anything unless we create a new migration file)

4. Generate a new migration file to add the description attribute to the books table. Enter the following:
```rails g migration add_description_to_books``` 
    - add the table, field and type in the migration file we just created
```ruby
class AddDescriptionToBooks < ActiveRecord::Migration[6.1]
  def change
            #  table  | field      |  type
    add_column :books, :description, :text
  end
end
```
    - Because we added a new migration file, enter ```rails db:migrate```
    - check to see if the schema updated from the latest migration 

#### Adding a model 
5. Create a file called book.rb under the models folder
6. Define a Book class and inherit from ApplicationRecord

```ruby 
class Book < ApplicationRecord
    
end
```

#### Rails console
7. Enter ```rails c``` in the console, and follow the lines of code.

    - Create a book and save to the database
```ruby
    Book.all
    Book.create(title: "first book", description: "Description of first article") 
    Book.all
```
    - create a book and save to the database with the new and save method
```ruby
    Book = Article.new
    Book.title = "second book"
    Book.description = "description of second article"
    Book.save
    Book.all
```
    - finding specific instances from the Books Tables
    - Updating attributes
    - Deleting Instances from the database with the destroy method
```ruby
    Book.first # gives you first instance from the Books table
    Book.all[0] # gives you the first instance from the Books table
    Book.find(1) # takes in an id and returns the specific book instance that holds id of 1
    book = Book.first 
    book.title = "Updated title"
    book.save # saves updated attributes
    book.last.destroy # deletes last instance from the Book Table
```

#### adding validations
8. In the book model class, validate the title and description attributes
```ruby
class Book < ApplicationRecord
    validates :title, presence:true
    validates :description, presence:true
end
```

9. Validate necessary lengths for both title and description
```ruby
class Book < ApplicationRecord
    validates :title, presence:true, length: {minimum: 2, maximum: 100}
    validates :description, presence:true, length: {minimum: 6, maximum: 200}
end
```

#### Adding a Book Controller + Views 
10. In the terminal, use the scaffold argument to generate the book controller and views

```rails g scaffold_controller Book```

- Couple things we need to update due to generating the model and view/controller at separate times. Scafold isn't smart enough to generate the attributes to the corresponding view/controllers. We must update them ourself.

11. Navigate to books/index.html.erb, create elements that correspond to the attributes of the Book Model as we iterate through the Books table 

- update the table headers
  ```html
    <thead>
    <tr>
      <th>Title</th>
      <th>Description</th>
      <th colspan="2"></th>
    </tr>
  </thead>
  ```
- output attributes title and description
```html
      <tr>
        <td><%= book.title %></td>
        <td><%= book.description %></td>
        <td><%= link_to 'Show', book %></td>
        <td><%= link_to 'Edit', edit_book_path(book) %></td>
        <td><%= link_to 'Destroy', book, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
```



12.  Navigate to books/_form.html.erb, create fields that correspond to the attributes of the Book Model as create the form.

```html
    <div class="field">
    <%= form.label :title %>
    <%= form.text_field :title %>
  </div>

  <div class="field">
    <%= form.label :description %>
    <%= form.text_area :description %>
  </div>

  <div class="actions">
    <%= form.submit %>
  </div>
```

13. Navigate to books/show.html.erb, outpute the attributes from the Book table

```html
<p>
  <strong>Title:</strong>
  <%= @book.title %>
</p>

<p>
  <strong>Description:</strong>
  <%= @book.description %>
</p>
```
14. Navigate to the books controller, under private, update the book_params to the following 
```ruby
    def book_params
      params.require(:book).permit(:title, :description)
    end
```
- permit limits the required parameters being passed in, for more additional info click [here](https://apidock.com/rails/ActionController/Parameters/permit)

- **<em>EXPLAIN</em> each defined method in the books controller and test out certain ORM methods in the rails console. 

#### Pages controller 
15. Generate a controller called pages 
```rails g controller Pages```

16. Define methods home and about in the pages controller 

```ruby 
    def home

    end

    def about 
        
    end
```

17. Create a root path that corresponds to the home method that exists in the book controller

```ruby 
Rails.application.routes.draw do
  root 'pages#home'
  resources :books
end

```

```ruby 
  root 'pages#home'
  get 'about', to: 'pages#about'
```

18. Create views for corresponding methods in the pages controller
  - under the views/pages, create a home.html.erb file and create an h1 element with content ```Home```
  - Create another file called about.html.erband create an h1 element with content ```About```

- run ```rails s``` in the terminal

**<em>NOTE</em> We don't have to render anything in the home and about methods, rails is smart enough to match the method names with the corresponding view file names in the file structure**



#### using byebug for debugging 
19. In the books controller, under the show method, include the byebug keyword (Note: We are able to use byebug because we have the byebug gem in our gemfile)

```ruby
  def show
    byebug
  end
```
20. Run the server, enter the following
  - localhost:4200/books
  - Select a book or create one if you don't see any
  - go to show and you should see your server pause in the terminal
  - In the terminal enter ```@book``` and you should see the contents of your book.


#### References
- https://blog.saeloun.com/2020/04/21/rails-adds-support-for-db-rollback-name-for-multiple-database-applications.html#:~:text=To%20revert%20such%20mistakes%20we,the%20latest%20migration%20was%20run.&text=We%20can%20also%20pass%20STEP,number%20of%20migrations%20to%20revert.
- <em>byebug</em> https://github.com/deivid-rodriguez/byebug