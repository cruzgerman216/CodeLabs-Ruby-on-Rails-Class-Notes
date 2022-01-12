  # Ruby on Rails - Devise and Project Setup
  ## PetMe Social Media App Part 1 Rails 6

## Topics Covered
- Devise Gem

### Devise setup
1. rails new pet_me_social_media_app
2. rails g devise:install. Follow the instructions. 
3. rails g devise:controllers && rails g devise User or rails g devise:user
4. rails db:migrate

### Routes 
1. In your routes.rb file include the following. 

```ruby
  root to: "pages#home"
```
2. rails g controller Pages home
3. Navigate to your controllers/application_controller.rb. Add the following 

```before_action :authenticate_user!``
- This will redirect the user to the sign in page if they are not logged in.


### Installing Bootstrap 5
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

### Adding Navigation Bar + Styling
1. Navigation to views/layouts and create a partial file called _navigation.html.erb
2. Copy/Paste the following Navbar from bootstrap 5
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container">
      <a class="navbar-brand" href="#">Navbar
      </a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
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

3. Replace the navbar brand to the following 

```html
      <a class="navbar-brand" href="#">PetMe Social 
        <svg width="1rem" version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 119.72 122.88" style="enable-background:new 0 0 119.72 122.88" xml:space="preserve"><style type="text/css">.st0{fill-rule:evenodd;clip-rule:evenodd;}</style><g><path class="st0" d="M40.06,0.37c9.4,0,17.03,11.69,17.03,26.1s-7.63,26.1-17.03,26.1c-9.4,0-17.03-11.68-17.03-26.1 C23.04,12.06,30.66,0.37,40.06,0.37L40.06,0.37z M61.71,63.55c19.94,0.04,22.42,13.25,39.23,35.86 c8.38,16.45-2.5,26.82-21.15,22.38c-8.46-4.31-14.41-5.83-20.38-5.63c-10.34,0.36-12.95,7.18-24.98,6.7 c-9.28-0.25-13.46-4.14-14.27-10.07c-0.87-6.3,1.56-10.28,4.52-15.49C36.18,77.02,48.07,61.01,61.71,63.55L61.71,63.55L61.71,63.55 z M7.17,39.08C0.14,41.86-2.1,52.85,2.16,63.62C6.42,74.39,15.57,80.87,22.6,78.09c7.03-2.78,9.27-13.77,5.01-24.54 C23.35,42.78,14.2,36.3,7.17,39.08L7.17,39.08z M112.55,39.08c7.03,2.78,9.27,13.77,5.01,24.54 c-4.26,10.77-13.42,17.25-20.44,14.47c-7.03-2.78-9.27-13.77-5.01-24.54C96.37,42.78,105.52,36.3,112.55,39.08L112.55,39.08z M79.35,0c9.4,0,17.03,11.69,17.03,26.1s-7.63,26.1-17.03,26.1c-9.4,0-17.03-11.68-17.03-26.1C62.33,11.69,69.95,0,79.35,0L79.35,0 z"/></g></svg>
      </a>
```

4. Sign up a user. Let's re-adjust a navlink item or add a new one so we can add log out functionality.

```html 
          <li class="nav-item">
            <%= link_to "Sign Out", class: "nav-link"%>
          </li>
```

- When the user clicks this navlink, we like to said a delete request to `destroy_user_session_path`. Devise gives us these helper methods, you can always view these by going to `rails routes` in the terminal. 
```html
            <%= link_to "Sign Out", destroy_user_session_path, method: :delete, class: "nav-link" if user_signed_in?%>

```
- We like to add a conditional only if the user is signed in. Devise uses user_signed_in?


```html 
          <% if user_signed_in? %>
          <li class="nav-item">
            <%= link_to "Sign Out", destroy_user_session_path, method: :delete, class: "nav-link"%>
          </li>
          <% end %>
```

5. Add a edit profile link. We will use the helper method `edit_user_registration_path` along with `current_user`. Again these methods were created by devise. 

```html
          <% if user_signed_in? %>
          <li class="nav-item">
            <%= link_to "Sign Out", destroy_user_session_path, method: :delete, class: "nav-link"%>
          </li>
          <li class="nav-item">
            <%= link_to "Edit Profile", edit_user_registration_path(current_user), class: "nav-link"%>
          </li>
          <% end %>
```

6. Remove excessive links and drop down

```html 
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container">
      <a class="navbar-brand" href="#">PetMe Social 
        <svg width="1rem" version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 119.72 122.88" style="enable-background:new 0 0 119.72 122.88" xml:space="preserve"><style type="text/css">.st0{fill-rule:evenodd;clip-rule:evenodd;}</style><g><path class="st0" d="M40.06,0.37c9.4,0,17.03,11.69,17.03,26.1s-7.63,26.1-17.03,26.1c-9.4,0-17.03-11.68-17.03-26.1 C23.04,12.06,30.66,0.37,40.06,0.37L40.06,0.37z M61.71,63.55c19.94,0.04,22.42,13.25,39.23,35.86 c8.38,16.45-2.5,26.82-21.15,22.38c-8.46-4.31-14.41-5.83-20.38-5.63c-10.34,0.36-12.95,7.18-24.98,6.7 c-9.28-0.25-13.46-4.14-14.27-10.07c-0.87-6.3,1.56-10.28,4.52-15.49C36.18,77.02,48.07,61.01,61.71,63.55L61.71,63.55L61.71,63.55 z M7.17,39.08C0.14,41.86-2.1,52.85,2.16,63.62C6.42,74.39,15.57,80.87,22.6,78.09c7.03-2.78,9.27-13.77,5.01-24.54 C23.35,42.78,14.2,36.3,7.17,39.08L7.17,39.08z M112.55,39.08c7.03,2.78,9.27,13.77,5.01,24.54 c-4.26,10.77-13.42,17.25-20.44,14.47c-7.03-2.78-9.27-13.77-5.01-24.54C96.37,42.78,105.52,36.3,112.55,39.08L112.55,39.08z M79.35,0c9.4,0,17.03,11.69,17.03,26.1s-7.63,26.1-17.03,26.1c-9.4,0-17.03-11.68-17.03-26.1C62.33,11.69,69.95,0,79.35,0L79.35,0 z"/></g></svg>
      </a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <% if user_signed_in? %>
          <li class="nav-item">
            <%= link_to "Sign Out", destroy_user_session_path, method: :delete, class: "nav-link"%>
          </li>
          <li class="nav-item">
            <%= link_to "Edit Profile", edit_user_registration_path(current_user), class: "nav-link"%>
          </li>
          <% end %>
      </div>
    </div>
  </nav>
  ```

### Styling Form

1. Navigate to views/devise/sessions, style the form using bootstrap 5. 

```html
<div style="margin: 0 25%; width: 50%">
  <h2>Log in</h2>

<%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>
  <div class="mb-3">
    <%= f.label :email, class: "form-label" %><br />
    <%= f.email_field :email, autofocus: true, autocomplete: "email", class:"form-control"%>
  </div>

  <div class="field">
    <%= f.label :password, class: "form-label" %><br />
    <%= f.password_field :password, autocomplete: "current-password", class:"form-control" %>
  </div>

  <% if devise_mapping.rememberable? %>
    <div class="field">
      <%= f.check_box :remember_me, class: "form-check-input" %>
      <%= f.label :remember_me %>
    </div>
  <% end %>

  <div class="actions">
    <%= f.submit "Log in", class:"btn btn-primary" %>
  </div>
<% end %>

<%= render "devise/shared/links" %>
</div>
```

- Repeat the same process for registrations/new.html.erb, registrations/edit.html.erb

