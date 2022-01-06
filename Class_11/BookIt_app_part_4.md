  # Ruby on Rails - Styling
  ## BookIt App Part 4

## Topics covered
  - ***<em>Bootstrap</em>** is a CSS framework that enables the process of rapid design to be easy.

#### Installing Bootstrap 5
- NOTE: Most of this section relies on CSS Knowledge. Feel free to change the design.

1. Add bootstrap 5 to the application by entering `yarn add bootstrap jquery @popperjs/core` for mac or `npm install bootstrap jquery @popperjs/core` for windows.
- navigate to package.json and you should see the latest version of bootstrap 5 installed
```javascript
    "@popperjs/core": "^2.11.0",
    "@rails/actioncable": "^6.0.0",
    "@rails/activestorage": "^6.0.0",
    "@rails/ujs": "^6.0.0",
    "@rails/webpacker": "5.4.3",
    "bootstrap": "^5.1.3",
    "jquery": "^3.6.0",
    "turbolinks": "^5.2.0",
```
2. Navigate to app/assets/stylesheets/application.css and require bootstrap

```
 *= require bootstrap
 *= require_tree .
 *= require_self
 */
```
3. Navigate to config/webpack/environment.ts. Copy and paste the following 

```typescript
const { environment } = require('@rails/webpacker')

const webpack = require("webpack")

environment.plugins.append("Provide", new webpack.ProvidePlugin({
    $: 'jquery',
    jQuery: 'jquery',
    Popper: ['popper.js', 'default']  // Not a typo, we're still using popper.js here
}))

module.exports = environment
```
4. Navigate app/assets/javascript/packs/application.js and import bootstap

```ts
import * as ActiveStorage from "@rails/activestorage"
import "channels"
import 'bootstrap'
```
5. Navigate to app/assets/stylesheets and create custom.css.scss. To overwrite bootstrap classes, include 
```css
 @import 'bootstrap/dist/css/bootstrap';
```

#### Adding a Navbar
6. Navigate to app/views/layouts and create a new file called _navigation.html.erb. Copy and paste the code

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark" >
  <div class="container-fluid">
    <a class="navbar-brand" href="#" style="font-family:serif; font-size: 2rem; font-weight:bold">Book It</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <a class="nav-link active" aria-current="page" href="#">Home</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">Link</a>
        </li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            Dropdown
          </a>
          <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
            <li><a class="dropdown-item" href="#">Action</a></li>
            <li><a class="dropdown-item" href="#">Another action</a></li>
            <li><hr class="dropdown-divider"></li>
            <li><a class="dropdown-item" href="#">Something else here</a></li>
          </ul>
        </li>
        <li class="nav-item">
          <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
        </li>
      </ul>
      <form class="d-flex">
        <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn btn-outline-success" type="submit">Search</button>
      </form>
    </div>
  </div>
</nav>
```

7. Transform the Book It application into a link that navigates the user to the root path of the application. Use embedded tags. 

```
    <%= link_to "Book It", root_path, class: "navbar-brand", style:"font-family: arial; font-size: 2rem; font-weight; bold" %>
```
8. render _navigation.html.erb in the application.html.erb file

```
    <%= render 'layouts/navigation' %>
    <%= render 'layouts/messages' %>
    <%= yield %>
```

#### Styling Home
9. Navigate to assets/stylesheets/pages.scss and copy/paste the following

```css
@import 'bootstrap/dist/css/bootstrap';

.home-background{
    padding: 5% 2%; 
    background-size: 100% 100%; 
    background-image: url('https://www.iworkinsport.com/iwis-admin/uploads/427545862167656.jpg'); 
    height: 90vh;
    width:100%;
}
.home-header{
    color:white;
    font-size:10vw
}
.home-paragraph{
    color:white; font-size:  1.1vw
}
.home-button{
    display:block; 
    width: 25%; 
    padding: 1% 0%; 
    font-size: 2rem; 
    margin: .5% 0%;
}

.explore{
    color: rgb(85, 85, 85);
    background-color: rgb(236, 236, 236);
    border: 1px solid rgb(187, 187, 187); 
    opacity: 90%;
}

@media screen and (max-width: 1170px) {
    .home-background{
        padding: 5% 5%; 
        background-size: 100% 100%; 
        background-image: url('https://www.iworkinsport.com/iwis-admin/uploads/427545862167656.jpg'); 
        height: 91vh;
        width:100%;
    }
    .home-header{
        color:white;
        font-size:10vw
    }
    .home-paragraph{
        color:white; font-size:  3vw;
        text-align:left;
        margin: 0 0 10% 0;
        width: 90%
    }
    .home-button{
        display:block; 
        width: 50%; 
        padding: 3% 0%; 
        font-size: 2rem; 
        margin: 5% 0%
    }
    .explore{
        color: rgb(85, 85, 85);
        background-color: rgb(236, 236, 236);
        border: 1px solid rgb(187, 187, 187); 
        opacity: 95%;
    }
  }


  @media (max-height: 750px) and (max-width: 1024px) {
    .home-background{
        background-size: 100% 100%; 
        background-image: url('https://www.iworkinsport.com/iwis-admin/uploads/427545862167656.jpg'); height: 90vh;
        width:100%;
    }
    .home-header{
        color:white;
        font-size:7vw;
        text-align:left;
        margin: 0 0 2% 0;
    }
    .home-paragraph{
        color:white; font-size:  2vw;
        text-align: left;
        margin: 0 0 5% 0;
    }
    .home-button{
        display:block; 
        width: 100%; 
        padding: 3% 0%; 
        font-size: 2rem; 
        margin: 5% 0%
    }
    .explore{
        color: rgb(85, 85, 85);
        background-color: rgb(236, 236, 236);
        border: 1px solid rgb(187, 187, 187); 
        opacity: 95%;
    }
  }

  @media screen and (max-width: 970px) {
    .home-background{
        padding: 5% 5%; 
        background-size: 100% 100%; 
        background-image: url('https://www.iworkinsport.com/iwis-admin/uploads/427545862167656.jpg'); height: 90vh;
        width:100%;
    }
    .home-header{
        color:white;
        font-size:10vw
    }
    .home-paragraph{
        color:white; font-size:  3vw;
        text-align:left;
        margin: 0 0 10% 0;
        width: 90%
    }
    .home-button{
        display:block; 
        width: 50%; 
        padding: 3% 0%; 
        font-size: 2rem; 
        margin: 5% 0%
    }
    .explore{
        color: rgb(85, 85, 85);
        background-color: rgb(236, 236, 236);
        border: 1px solid rgb(187, 187, 187); 
        opacity: 95%;
    }
  }

  @media screen and (max-width: 750px) and (max-height:1024px) {
    .home-background{
        padding: 10% 5%; 
        background-size: 100% 100%; 
        background-image: url('https://www.iworkinsport.com/iwis-admin/uploads/427545862167656.jpg'); 
        height: 95vh;
        width:100%;
    }
    .home-header{
        color:white;
        font-size:10vw;
        text-align:center;
        margin: 0 0 10% 0;
    }
    .home-paragraph{
        color:white; font-size:  5vw;
        text-align:center;
        margin: 0 0 20% 0;
    }
    .home-button{
        display:block; 
        width: 100%; 
        padding: 3% 0%; 
        font-size: 2rem; 
        margin: 5% 0%
    }
    .explore{
        color: rgb(85, 85, 85);
        background-color: rgb(236, 236, 236);
        border: 1px solid rgb(187, 187, 187); 
        opacity: 95%;
    }
  }
```

10. In views/pages/home.html.erb, let's add an intro image/header/paragraph and related user buttons
```html
    <div class="home-background" >
        <h1 class="home-header">Discover a New Journey</h1>
        <p class="home-paragraph">The Book It app allows you to create, update and delete any books in your personal bookshelf. Join today by signing up.</p>

        <button class="btn btn-success btn-lg home-button" >Sign Up </button>
        <button class=" home-button explore" ><a href="#library" style="color:rgb(85, 85, 85); text-decoration:none;">Explore Library</a> </button>
    </div>
```

11. Let's add another section called library. This section will contain a header and cards containig book information. Copy and paste the following 
```html
<h1  style="font-size: 5rem; text-align:center" id="library">Popular Books</h1>
<div class="container" style="padding: 5% 0%">
    <div class="row"> 
        <div class="col-md-3">
            <div class="card">
                <img src="https://i.ebayimg.com/images/g/SSIAAOSwmlpf~f~5/s-l500.jpg" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">Card title</h5>
                    <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This card has even longer content than the first to show that equal height action.</p>
                </div>
                <div class="card-footer">
                    <small class="text-muted">Last updated 3 mins ago</small>
                </div>
            </div>       
        </div>
                <div class="col-md-3">
            <div class="card">
                <img src="https://i.ebayimg.com/images/g/SSIAAOSwmlpf~f~5/s-l500.jpg" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">Card title</h5>
                    <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This card has even longer content than the first to show that equal height action.</p>
                </div>
                <div class="card-footer">
                    <small class="text-muted">Last updated 3 mins ago</small>
                </div>
            </div>       
        </div>
         <div class="col-md-3">
                <div class="card">
                    <img src="https://i.ebayimg.com/images/g/SSIAAOSwmlpf~f~5/s-l500.jpg" class="card-img-top" alt="...">
                    <div class="card-body">
                        <h5 class="card-title">Card title</h5>
                        <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This card has even longer content than the first to show that equal height action.</p>
                    </div>
                    <div class="card-footer">
                        <small class="text-muted">Last updated 3 mins ago</small>
                    </div>
                </div>       
        </div>
                 <div class="col-md-3">
                <div class="card">
                    <img src="https://i.ebayimg.com/images/g/SSIAAOSwmlpf~f~5/s-l500.jpg" class="card-img-top" alt="...">
                    <div class="card-body">
                        <h5 class="card-title">Card title</h5>
                        <p class="card-text">This is a wider card with supporting text below as a natural lead-in to additional content. This card has even longer content than the first to show that equal height action.</p>
                    </div>
                    <div class="card-footer">
                        <small class="text-muted">Last updated 3 mins ago</small>
                    </div>
                </div>       
        </div>
    </div>
</div>
```

12. At the bottom, include a footer.

```html
<div class="footer text-center" style="background-color:rgb(51, 51, 51); color: white; padding: 1% 0%; position:sticky; width:100%; bottom: 0; position: fixed;">Copyright © <a style="color: white" href="https://github.com" target="_blank">Book It</a></div> 
```

#### Adding more Layout Links 
13. Navigate to _navigation.html.erb, alter the home navlink to `Books` and use embedded ruby tags to navigate to the books path. 

```
          <%= link_to "Books", books_path, class: "nav-link active" %>
```

#### Styling Books Index Page
14. Navigate to views/books/index.html.erb. Get rid of all content in the file.
- At the top include the following heading, follow these series elements

```html
<h1  style="font-size: 5rem; text-align:center" id="library">Library</h1>
<div class="container">
    <div class="row"> 

    </div>
</div>
```

15.  Within the row div, use embedded tags to iterate through each book record. As we iterate through each book, we can include a col-md-3 div for each record. This class will allow each element to be equally spaced. Use the instance variable and embedded tags to output title and description.

```html
        <% @books.each do |book| %>
        <div class="col-md-3">
            <div class="card">
                <img src="https://i.ebayimg.com/images/g/SSIAAOSwmlpf~f~5/s-l500.jpg" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title"><%= book.title%></h5>
                    <p class="card-text"><%= book.description%></p>
                </div>
                <div class="card-footer">
                    <small class="text-muted">Last updated 3 mins ago</small>
                </div>
            </div>  
        </div>     
        <% end %>
```

16. Below the embbedded tags include buttons edit, show, delete. 

```html
                    <h5 class="card-title"><%= book.title%></h5>
                    <p class="card-text"><%= book.description%></p>
                    <button class="btn btn-primary"><%= link_to 'Edit', edit_book_path(book), class:"link" %></button>
                    <button class="btn btn-secondary"><%= link_to 'Show', book_path(book), class:"link" %></button>
                    <button  class="btn btn-danger" ><%= link_to 'Delete', book_path(book), method: :delete, class:"link" %></button>
```

- Link class 
```css
.link{
    color:white;
    text-decoration: none
}
```

17.  Include Create button.

```html
    <button class="btn btn-success" style="margin: 5% 0%"> <%= link_to 'Create Book', new_book_path, class:"link" %></button>
```

#### Styling Form Partial
18. Use bootstrap to style the form partial

```html
<% if @book.errors.any? %>
        <% @book.errors.full_messages.each do |message| %>
        <p><%= message %></p>
        <% end %>
<% end %>

<div style="margin: 0 25%; width:50%"> 
<%= form_with(model: @book) do |form| %>
        <div class="mb-3"> 
        <%= form.label :title, "Title:", class:"form-label" %>
        <%= form.text_field :title, class:"form-control" %>
        </div>
        <div class="mb-3">
        <%= form.label :description, "Description:", class:"form-label" %>
        <%= form.text_area :description, class:"form-control" %>
        </div>
        <%= form.submit "Update Book", class:"btn btn-primary" %>
<% end %>
</div>
```

19. Navigate to edit.html.erb and add the class text-center to the h1 element. Style the link tag.

```html
<h1 class="text-center">Edit Book</h1>

<%= render 'form' %> 
<div class="text-center"><%= link_to '[Cancel and go back to books index]',
    books_path %></div>

```

20. Navigate to new.html.erb and add the class text-center to the h1 element

```html
<h1>Create a new Book</h1>
```

#### Styling messages
21. Navigate to _form.html.erb and include the following in-line styles to the error messages.

```html
<% if @book.errors.any? %>
        <ul class="alert alert-warning alert-dismissible fade show" >
                <h3>The following errors prevented the book to be saved</h3>
                <% @book.errors.full_messages.each do |message| %>
                <li style="color:red; margin: 1%"><%= message %></li>
                <% end %>
                <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </ul>
<% end %>
```

22. Create a file called _errors.html.erb under views/shared

23. Render the errors partial file in _form.html.erb

```
<div style="margin: 0 25%; width:50%"> 
        <%= render 'shared/errors' %>
        <%= form_with(model: @book) do |form| %>
                <div class="mb-3"> 
```

24.  Navigate to _messages.html.erb, use bootstrap to style the embbedded tags. 

```html
      <%  flash.each do |name, message| %> 
      <ul class="alert alert-success alert-dismissible fade show" >
        <%= message %>
            <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </ul>
      <% end %>
```

#### Styling Show page

25. navigate to show.html.erb, center the h1 element
```html
<h1 class="text-center">Showing Book details</h1>
<%= flash[:notice]%>
```

26. Use Bootstrap to style book details

```html
<div class="container">
  <div class="card" style="margin: 10% 25%">
    <img
      src="https://i.ebayimg.com/images/g/SSIAAOSwmlpf~f~5/s-l500.jpg"
      class="card-img-top"
      alt="..."
    />
    <div class="card-body">
      <h5 class="card-title"><%= @book.title%></h5>
      <p class="card-text"><%= @book.description%></p>
      <button class="btn btn-primary">
        <%= link_to 'Edit', edit_book_path(@book), class:"link" %>
      </button>
      <button class="btn btn-secondary">
        <%= link_to 'Return to books page', books_path, class:"link"%>
      </button>
      <button class="btn btn-danger">
        <%= link_to 'Delete', book_path(@book), method: :delete, class:"link" %>
      </button>
    </div>
    <div class="card-footer">
      <small class="text-muted">Last updated 3 mins ago</small>
    </div>
  </div>
</div>
```

#### Footer

27. Create _footer.html.erb under layouts and copy and paste the footer element that exists in home.html.erb to the corresponding file.

28. Render the file in application.html.erb

```html
  <body>
    <%= render 'layouts/navigation' %>
    <%= render 'layouts/messages' %>
    <%= yield %>
    <%= render 'layouts/footer' %>
  </body>
```


#### More Styling

29. Navigate to application.html.erb, and color the background of the body element.

```css
body{
    background-color: rgb(243, 243, 243);
}
```

30. Navigate to layouts/_navigation.html.erb and include drop down items 

```html
          <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
            <li><%= link_to "Books", books_path, class: "dropdown-item"%></li>
            <li><%= link_to "Create Book", new_book_path, class: "dropdown-item"%></li>
          </ul>
```
- get rid of the books nav link

#### Adding the image attribute to the books table

31. In the terminal, generate a migration file that adds the image_path attribute to the books table.

```rails generate migration add_imagePath_to_books image_path:string```
- run ```rails db:migrate```

32. Navigate to the books_controllers.rb file, include the image_path attribute in the required params.

```
    def book_params
        params.require(:book).permit(:title, :description, :image_path)
    end
```

33. Navigate to views/books/_form.html.erb, add a label and field for the image_path attribute.

```html
  <div class="mb-3">
    <%= form.label :image_path, "Image URL:", class:"form-label" %> <%=
    form.text_area :image_path, class:"form-control" %>
  </div>
```
34. Navigate to index.html.erb, and get rid of the img element. Use the built in rails image_tag to produce the img element follow by the image source and class.

```html
          <%= image_tag book.image_path, class: "card-img-top" %>
```
- You will get errors in case your image_path is nil, reset your db by running `rails db:reset` follow by `rails db:migrate`.

35. In books_controller.rb, check to see if the image url is valid using `net/http`. Create a method called image_exists? that has one paramter called image_path. In this method, use `net/http` to check the image_path is a valid URL as well as its content to be an image.

```ruby
  def image_exists?(image_path)
    require "net/http"
    if(image_path == nil || image_path.starts_with?("http") == false || image_path.starts_with?("https") == false)
        return false
    end
    url = URI.parse(image_path)
    req = Net::HTTP.new(url.host, url.port)
    if (image_path.starts_with?("https"))
      req.use_ssl = true
    end
    res = req.request_head(url.path)
    return res.code == "200" && res.content_type.starts_with?("image")
  end
```

36. In the create method, check to see if the image path is valid if not, store a valid image as its replacement.

```ruby
  def create
    @book = Book.new(book_params)
    if(image_exists?(book_params[:image_path]) == false)
        @book.image_path = "https://upload.wikimedia.org/wikipedia/commons/b/b9/No_Cover.jpg"
    end
```

37. Navigate to views/books/show.html.erb, replace the img element with the built in image_tag provided by rails.

```ruby
    <%= image_tag @book.image_path, class: "card-img-top" %>

```

#### Updating the home page books

38. Navigate to helpers/pages_helper.rb. In module PagesHelper, define a method called get_books that will return the first 8 books from the books table.

```ruby
module PagesHelper
    def get_books
return  Book.first(8)
    end
end
```
- we can be more creative with this and get 8 random books from our database

```ruby
  def get_books
    return 8.times.map { Book.all[Random.rand(Book.count)] }
  end
```
- What happens if our Database is inintally empty? We like to return an empty array if that is the case 
```ruby
    if Book.all.count == 0
        return []
    elsif
        return  8.times.map { Book.all[Random.rand(Book.count)]}
    end
```
- In a another use case, what happens if we have more than 1 and less than 8 book records? Our logic wouldn't make sense. So let's return books if it fits that condition.
```ruby
    if Book.all.count == 0
        return []
    elsif Book.all.count < 9 
        return Book.all.count.times.map{Book.all[Random.rand(Book.count)]}
    else
        return  8.times.map { Book.all[Random.rand(Book.count)]}
    end
```
- Try refactoring this as an exercise!

1.   Navigate to views/layouts/pages/home.html.erb, get rid of the card selection and instead copy and paste the following from your index.html.erb:

- Get rid of button elements

```html
<div class="container" style="padding: 5% 0%">
  <div class="row">
    <% @books.each do |book| %>
    <div class="col-md-3">
      <div class="card">
          <%= image_tag book.image_path, class: "card-img-top" %>
        <div class="card-body">
          <h5 class="card-title"><%= book.title%></h5>
          <p class="card-text"><%= book.description%></p>
        </div>
        <div class="card-footer">
          <small class="text-muted">Last updated 3 mins ago</small>
        </div>
      </div>
    </div>
    <% end %>
  </div>
</div>
```

- instead of iterating through @books, replace @books with the get_books method. This will allow us to iterate through the first 8 records that exists in the database.

```html
<div class="container" style="padding: 5% 0%">
  <div class="row">
    <% get_books().each do |book| %>
    <div class="col-md-3">
      <div class="card">
```

#### Deploying changes
- You can always add automatic builds to your repo's main branch. Here we will use the command line to push those changes

  - Enter `git push heroku master`
  - Enter `heroku run rails db:migrate`
  - Let's wipe out the database to accomodate the new attribute image_path and include a seed file.
- Enter `heroku run rails c`, this will allow you to run ORM commands in the terminal that has your production database on Heroku.
- enter `Book.destroy_all` this will destroy all the books (NOTE: This is only for demonstration purposes, normally you do NOT do this for production).
- exit the terminal

#### Seed file 
- I want to be able to output many records without manually doing it. I will use programming basics to iterate from 1 - 100 to create book records in db/seeds.rb. I will use the faker gem to help me store data.

43. Navigate to the Gemfile and include the faker gem in development and test. Run `bundle install`

```ruby
group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
  # Use sqlite3 as the database for Active Record
  gem 'sqlite3', '~> 1.4'
  gem 'faker', :git => 'https://github.com/faker-ruby/faker.git', :branch => 'master'
end
```

44. Navigate to db/seeds.rb. Require faker at the top of the file

```ruby
require 'faker'
```

45. Create a local variable called images that contain 4 image address(you can google these).

```ruby
require 'faker'
images = [
    "https://image.shutterstock.com/image-photo/blue-background-texture-260nw-1552346156.jpg",
    "https://img.freepik.com/free-vector/hand-painted-watercolor-pastel-sky-background_23-2148902771.jpg?size=626&ext=jpg",
    "https://thumbs.dreamstime.com/z/beautiful-autumn-foliage-background-brunches-falling-tree-leaves-sky-bokeh-98280795.jpg",
    "https://i.pinimg.com/474x/80/38/ea/8038ea15024f5c34926bf7418999b573.jpg",
]
```

46. Iterate from 1 to 100 and create a Book record using Faker. Use the built in Random class to randomly get an image address for each record.

```ruby
require 'faker'
images = [
    "https://image.shutterstock.com/image-photo/blue-background-texture-260nw-1552346156.jpg",
    "https://img.freepik.com/free-vector/hand-painted-watercolor-pastel-sky-background_23-2148902771.jpg?size=626&ext=jpg",
    "https://thumbs.dreamstime.com/z/beautiful-autumn-foliage-background-brunches-falling-tree-leaves-sky-bokeh-98280795.jpg",
    "https://i.pinimg.com/474x/80/38/ea/8038ea15024f5c34926bf7418999b573.jpg",
]
(1..100).each do |n|
    Book.create(title: Faker::Book.title, description: Faker::Lorem.paragraph, image_path: images[Random.rand(4)] )
end
```

- run `rails db:seed`

47. Push your changes to github and heroku `git push heroku master`
48. Run `heroku run bundle install` 
49. Seed the heroku db by running `heroku run rails db:seed`
- You will get an error due to faker being available to test and development. You can change this to production to run this command. However, I will keep it this way to set an example.


