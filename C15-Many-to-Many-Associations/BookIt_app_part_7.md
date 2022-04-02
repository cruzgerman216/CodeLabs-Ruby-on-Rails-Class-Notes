# BookIt App Part 7 - Many to Many Associations and Automated Testing

### Table of Contents

- <a href="#test-models">Testing Models and Creating Categories</a>
- <a href="#test-controllers">Categories Controller and Tests</a>
- <a href="#many-to-many-association">Many to Many Assocation</a>

---

## Topics Covered

- Many to Many Associations
- Testing
  - Automated
  - Functional
  - Integration

<div id="test-models"></div>

## Testing models and Creating Categories

Navigate to `test/models`. We will go ahead and test out our model validations. We haven't yet created out `category` model but we will do so after defining a few test cases.

Create `category_test.rb`. Require `test_helper` at the top of the file and define a class called `CategoryTest` that inherits `AciveSupport::TestCase`.

```ruby
require 'test_helper'
class CategoryTest < ActiveSupport::TestCase
end
```

Run `rails test` NOTE: You may get several errors from other files. In my case, I had multiple test files under `test/controllers`. Always look at the console to see where the errors are coming from and figure out how to solve them or delete/get rid of the test cases or files. Either or.

In order to create a test block, you must include the keyword `test` follow by the description of the test.

```ruby
test "category should be valid" do
end
```

Create a new Category instance and use the keyword `assert` to see if the creation of the value is valid.

```ruby
    test "category should be valid" do
        @category = Category.new(name: "Sports")
        assert @category.valid?
    end
```

Run `rails test` and we get an error.

```
Error:
CategoryTest#test_category_must_be_valid:
NameError: uninitialized constant CategoryTest::Category
    test/models/category_test.rb:5:in `block in <class:CategoryTest>'
```

That's because we haven't added our categories table and defined our model just yet!

Navigate to `app/models` and `create category.rb`

```ruby
class Category < ApplicationRecord
end
```

Run `rails generate migration create_categories name:string`
Run `rails db:migrate`
Run `rails test`

## Validations Using Unit Tests

Navigate to `test/model/category_test.rb`. Let's test the following tests

```ruby
    test "name should be present" do

    end

    test "name should be unique" do

    end

    test "name should be not too long" do

    end

    test "name should be not too short" do

    end
```

Let's create a category without a name. Of course we will have these failed validations as we haven't implemented any validations to the category model.

```ruby
test "name should be present" do
    @category = Category.new(name: " ")
    assert_not @category.valid?
end
```

Run `rails test`

To fix this error navigate to `models/category.rb` and implement the validation that requires name to not be empty.

```ruby
class Category < ApplicationRecord
    validates :name, presence: true
end
```

To get rid of DRY code, define a method called `setup` to create the category instance. `setup` is a reserved method that will execute before every test does.

```ruby
    def setup
        @category = Category.new(name: "Sports")
    end

    test "category should be valid" do
        # No longer need to declare @category
        assert @category.valid?
    end

    test "name should be present" do
        @category.name = " "
        assert_not @category.valid?
    end
```

Names should be unique. Save @category and create a new instance with the same name.

```ruby
    test "name should be unique" do
        @category.save
        @category2 = Category.new(name: "Sports")
        assert_not @category2.valid?
    end
```

Because our test fails, navigate to `models/category.rb` and let's enforce the unique validation and then run `rails test` to see passing tests.

```ruby
class Category < ApplicationRecord
    validates :name, presence: true, uniqueness: true
end
```

Note: Tests shouldn't be too long. Notice how we are separating our test cases into small chunks.

The next test: limit the characters to 3 through 26. Let's test this validation.

```ruby
    test "name should be not too long" do
        @category.name = "a" * 26
        @category.save
        assert_not @category.valid?
    end

    test "name should be not too short" do
        @category.name = "aa"
        @category.save
        assert_not @category.valid?
    end
```

Run `rails test`. You should get two failed tests.

- To pass these tests, add their validations to `models/category.rb`.

```ruby
class Category < ApplicationRecord
    validates :name, presence: true, uniqueness: true, length: {minimum: 3, maximum: 25}
end
```

<div id="test-controllers"></div>

## Categories Controller and Tests

Let's use scaffold to create the rest of our tests for category.
Run `rails g test_unit:scaffold category`
Comment all each test except index, new, show
Update the `setup` method to give category a specific name of "Sports"

```ruby
  setup do
    @category = Category.create(name: "Sports")
  end
```

Run `rails test test/controllers/categories_controller_test` to specify the test.

We fail our tests. Let's resolve them by defining category routes in `route.rb`.

```ruby
  delete 'logout', to: 'sessions#destroy'
  resources :categories, except: [:destroy]
```

Create a `categories_controller.rb` file under `controllers`.

```ruby
class CategoriesController < ApplicationController
end
```

Define `new`, `create`, `index`, and `show` methods.

```ruby
class CategoriesController < ApplicationController
    def new

    end

    def create

    end

    def index

    end

    def show
    end
end
```

Run `rails test`. We get specific errors referring to missing templates for `new`, `index` and `show`.

Create templates for each corresponding action: `new`, `index`, `show`. Under views, create a `categories` folder. In categories, create `index.html.erb`, `new.html.erb`, `show.html.erb`

Navigate to `test/controllers/categories_controllers_test.rb` and update the `show` test case.

```
  test "should show category" do
    @category.save
    get category_url(@category)
    assert_response :success
  end
```

Run `rails test` and all tests should pass.

## Test creation of Categories

Navigate to `views/categories/new.html.erb`. Create a form using `form_with` and create a label and text field for the `name` attribute.

```html
<h1 class="text-center">New Category Creation form</h1>

<div style="margin: 0 25%; width: 50%">
  <%= render 'shared/errors', obj: @category %> <%= form_with(model: @category)
  do |form| %>
  <div class="mb-3">
    <%= form.label :name, "Name:", class:"form-label", placeholder: "Enter a
    name" %> <%= form.text_field :name, class:"form-control" %>
  </div>
  <%= form.submit("Create Category", class:"btn btn-primary") %> <% end %>
</div>
```

Navigate to the `categories_controller.rb`, under the `new` method define an instance variable of category.

```ruby
class CategoriesController < ApplicationController
    def new
        @category = Category.new;
    end

```

In the `create` method, use the params hash to create a category as well category_params

```ruby
    def create
        @category = Category.new(category_params)
        if @category.save
            flash[:notice] = "Category was successfully created"
            redirect_to @category
        else
            flash[:notice] = "Category was not created succesffully"
            render 'new'
        end
    end
.
.
.
    private
    def category_params
        params.require(:category).permit(:name)
    end
```

Navigate to `categories_controller_test.rb` and uncomment the specific test with the description `should create category`. Be sure to include the name of the category in the params. After creating a category, we would like to redirect the user to the show page of that category.

```ruby
  test "should create category" do
    assert_difference('Category.count',1) do
      post categories_url, params: { category: { name: "Non-fiction" } }
    end

    assert_redirected_to category_url(Category.last)
  end
```

Run `rails test`

## Integration test

Create an integration test for category.

Run `rails g integration_test create_category`

Navigate to `test/integration/create_category_test.rb`. Create a test with the description "get new category form and create category

```ruby
class CreateCategoryTest < ActionDispatch::IntegrationTest
  test "get new category form and create category" do

  end
end
```

Use the key word `get` to send a `get request` to "/categories/new". Use assert_response to record the outcome of the request

```ruby
  test "get new category form and create category" do
    get "/categories/new"
    assert_response :success
    end
```

Use `assert_difference` to add to the Category count and use the `post` method to send a post request to the categories path.

```ruby
  test "get new category form and create category" do
    get "/categories/new"
    assert_response :success
    assert_difference "Category.count", 1 do
      post categories_path, params: { category: { name: "Sports" } }
      assert_response :redirect
    end
    follow_redirect!
    assert_response :success
    assert_match "Sports", response.body
  end
```

Navigate to `views/categories/show.html.erb` and include the category name in the html.

```html
<h1 class="text-center">Category: <%= @category.name %></h1>
```

Navigate to `controllers/categories_controller.rb`, under the `show` method, locate the category using params by id.

```ruby
    def show
        @category = Category.find(params[:id])
    end
```

Run `rails test`

### Integration test and feature: List categories

Generate an integration test to test out the categories index page `rails g integration_test list_categories`. Navigate to the file. Define `setup`. Create two categories instances.

```ruby
  def setup
    @category1 = Category.create(name: "Mystery")
    @category2 = Category.create(name: "Non-fiction")
  end
```

We will check the contents of `/categories` and test for contents of links of each category.

```ruby
  test "Should show category list" do
    get '/categories'
    assert_select "a[href=?]", category_path(@category1), text:@category1.name
    assert_select "a[href=?]", category_path(@category2), text:@category2.name
  end
```

To pass the test, navigate to `views/categories/index.html.erb`. Here is the content to successfully pass the test.

```html
<h1 class="text-center">List of Categories</h1>

<div class="container text-center">
  <% @categories.each do |category| %> <%= link_to category.name,
  category_path(category), style:"border: 1px solid black; width:50%; margin: 0
  25%; padding: 5%; display: block; font-size: 3rem;text-decoration: none" %> <%
  end %>
</div>
```

In `categories_controller.rb`, under the `index` method, store all category instances in an instance variable.

```ruby
    def index
        @categories = Category.all
    end
```

Run `rails test`

### Admin requirement and test

Navigate to `categories_controller_test.rb`. We will test for the following:

```ruby
  test "should not create category if not admin" do
    assert_no_difference('Category.count',1) do
      post categories_url, params: { category: { name: "Non-fiction" } }
    end

    assert_redirected_to categories_url
  end
```

Navigate to `categories_controller_test.rb`. We want to implement a method called `require_admin`, similiar to require_user, for actions other than `show` and `index`. We will use `before_action` to do this.

```ruby
class CategoriesController < ApplicationController
    before_action :require_admin, except: [:show, :index]
```

Define `require_admin` and check to see if current user is admin.

```ruby
    def require_admin
        if !(helpers.logged_in? && helpers.current_user.admin?)
            flash[:notice] = "Only admin can perform this action"
            redirect_to categories_path
        end
    end
```

Run `rails test`. We get several failed errors due to not simmulating the sign in phase for admin.

Navigate to test_helper.rb. Lets define a method that signs in the user.

```ruby
  def sign_in(user)
    post login_path, params: {session: {email: user.email, password: "Admin"}}
  end
```

Navigate to `integration/categories_controller_test.rb`, define the `setup` method and include a created user that is an admin. Make sure the password is "Admin". Include sign_in

```ruby
  setup do
    @category = Category.create(name: "Sports")
    @admin = User.create(username:"admin", email:"admin@gmail.com", password:"Admin", admin: true)
    sign_in(user)
  end
```

Invoke the `sign_in` methods for new and create tests.

```ruby
  test "should get new" do
    sign_in(@admin)
    .
    .
    .
      test "should create category" do
    sign_in(@admin)
```

Run `rails test/controllers/categories_controller_test.rb`

Navigate to `create_category_test.rb`. Define setup and use `sign_in` to verify a logged in admin user.

```ruby
  setup do
    @admin = User.create(username:"1admin", email:"1admin@gmail.com", password:"Admin", admin: true)
  end
  test "get new category form and create category" do
    sign_in(@admin)
```

Run `rails test`

### Update navigation

Navigate to `_navigation.html.erb`, and add a dropdown for categories similiar to books dropwdown.

```html
<li class="nav-item dropdown">
  <a
    class="nav-link dropdown-toggle"
    href="#"
    id="navbarDropdown"
    role="button"
    data-bs-toggle="dropdown"
    aria-expanded="false"
  >
    Categories
  </a>
  <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
    <li>
      <%= link_to "View Categories", categories_path, class: "dropdown-item"%>
    </li>
    <% if logged_in? && current_user.admin? %>
    <li>
      <%= link_to "Create Category", new_category_path, class: "dropdown-item"%>
    </li>
    <%end%>
  </ul>
</li>
```

<div id="many-to-many-association"></div>

## Many to Many Association

Create a migration file to create the association between book and categories `rails g migration create_book_categories`

```ruby
class CreateBookCategories < ActiveRecord::Migration[6.1]
  def change
    create_table :book_categories do |t|
      t.integer :book_id
      t.integer :category_id
      t.timestamps
    end
  end
end
```

- `rails db:migrate`

Under models, create` book_category.rb`.

```ruby
class BookCategory < ApplicationRecord

end
```

Add belongs_to to category and book to validate each upon creation.

```ruby
class BookCategory < ApplicationRecord
    belongs_to :book
    belongs_to :category
end
```

Navigate to books.rb and use the controller `through` to create the many associate.

```ruby
class Book < ApplicationRecord
    belongs_to :user
    has_many :book_categories
    has_many :categories, through: :book_categories
```

Navigate to category.rb and create the has_many association.

```ruby
    has_many :book_categories
    has_many :books, through: :book_categories
end
```

Let's test this in the console

- `rails c`
- `BookCategory.create(book_id: Book.first.id, category_id. Category.first.id)`
- `Book.first.categories` It should output categories associated with the book.

## Add association from UI

In `books_controler.rb`, update the book_params to accept more than 1 category ids.

```ruby
  def book_params
    params.require(:book).permit(:title, :description, :image_path, category_ids: [])
  end
```

Navigate to `books/_form.html.erb`. Let's add the categories dropdown.

```html
<div class="mb-3">
  <%= form.label :category, "Category:", class:"form-label" %>
  <%=form.collection_select(:category_ids, Category.order(:name), :id, :name,
  {prompt: "Select"}, {multiple: true, size: 3, class: "form-control"})%>
</div>
```

### Update book views to display categories

Create `_category.html.erb` under `views/categories`. The content of this file contains a category instance we've recieved that is being used as a link.

```html
<%= link_to category.name, category_path(category), class:"badge bg-info
text-dark" , style:"text-decoration: none" %>
```

Navigate to `books/show.html.erb`, here I render the category partial while iterating through the books categories.

```html
<div class="card-header">
  By <%= @book.user.username %> <% if @book.categories.any? %>
  <div><%= render @book.categories %></div>
  <% end %>
</div>
```

Navigate to `books/_books.html.erb`. Let's render the categories again.

```html
<div class="card-header">
  By <%= link_to book.user.username, user_path(book.user)%> <% if
  book.categories.any? %>
  <div><%= render book.categories %></div>
  <% end %>
</div>
```

### Deploy

Push to github
Enter `git push heroku master`
Enter `heroku run rails db:migrate`