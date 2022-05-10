# Book It Front End

---

### Table of Contents

- <a href="#getting-started">Getting Started</a>
- <a href="#signing-up">Sign Up</a>
- <a href="#signing-in">Sign In</a>
- <a href="#auto-login-and-bugs">Auto Login and Bugs</a>
- <a href="#authorizing-user-requests">Authorizing User Requests</a>
- <a href="#auto-fetching-books">Auto Fetching books</a>
- <a href="#saving-a-book-from-bookshelf">Saving a Book from Bookshelf</a>
- <a href="#saving-a-book-from-open-library">Saving a Book from Open Library</a>
- <a href="#deleting-books">Deleting Books</a>
- <a href="#Display-book-details">Display Book Details</a>
- <a href="#editing-books">Editing Books</a>
- <a href="#deploying">Deploying to Heroku</a>

---

<div id="getting-started"></div>

## Getting Started

Navigate to [Angular repo](https://github.com/NolanHovis/Angular-Bootcamp_Students-App)

- clone repo/open in github desktop
- open project in finder
  - delete .git(if you can't find .git, press shift + cmd + .)
- find and open the project in github desktop
- publish repo, open in vs code and run npm install
- Make sure you have the API running locally

<div id="signing-up"></div>

## Updating Sign Up

Navigate to _shared/auth/auth.service.ts_ to configure the authentication setting. Removing all Firebase URLs/logic.

Change Urls to the local server rails creates so we can test our Front End locally or you can use the deployed application. I will pick the deployed API.

```ts
const SIGN_UP_URL =
  "https://bookit-api-test-run.herokuapp.com/api/v1/users/create";
const SIGN_IN_URL =
  "https://bookit-api-test-run.herokuapp.com/api/v1/users/login";
```

Send a post request using the correct URL and remove the environment key as it's no longer needed. Also remove `returnSecureToken`. I went ahead and removed the pipe method and everything. This is mainly because I didn't want the user to be authenticated as soon as he/she signed up. In the future we can perhaps, send an email confirmed to make sure they own the email they are using.

```ts
  signUp(email: string, password: string) {
    return this.http
      .post<AuthResponseData>(SIGN_UP_URL, {
        email,
        password
      })
  }
```

Test to see if it works.

<div id="signing-in"></div>

## Signing In

In _shared/auth/auth.service.ts_, alter the `signIn` to the correct url and remove `returnSecureToken`

```ts
  signIn(email: string, password: string) {
    return this.http
      .post<AuthResponseData>(SIGN_IN_URL, {
        email,
        password,
      })
```

We are no longer dealing with the payload from firebase, we are dealing with the payload from the Book It API so we have to go ahead and make a few changes.

Make `res` of type `any`.

Grab `exipiry` and the token `value` from payload.

Convert the expiry value into JS date object and get the difference using `Date.now()` in milliseconds.

Convert the time

```ts
      .pipe(
        tap((res: any) => {
          const { expiry, value } = res.payload.token;
          const { email, id } = res.payload.user;

          const expirationTIme = new Date(expiry).getTime() - Date.now();
          this.handleAuth(email, id, value, +expirationTIme);
        })
      );
```

Test the sign in logic. After signing in, take a look at the api server and check to see if the request was successful. You can check whether or not a token was created upon login by going to the rails console and entering `User.first.tokens`.

### Messags and Navigating The User Upon Login

Adding messages allows the user to know what to do next. Navigate to `auth.component.ts`. Let's go ahead and define a new property called `msg`that is of type`string`and set to`null`.

```ts
errMsg: string = null;
msg: string = null;
```

Within `authObsrv`'s subscribe method, check to see if `isLoginMode` is true, if so, navigate the user to bookshelf, otherwise set `msg` to `Thank you for signing up! Please login.`. Then set `isLoginMode` to `true`.

```ts
    this.authObsrv.subscribe(
      (res) => {
        console.log('AUTH RESPONSE SUCCESS:', res);
        if (this.errMsg) this.errMsg = null;

        if (this.isLoginMode) {
          this.router.navigate(['bookshelf']);
        }else {
          this.msg = "Thank you for siging up! Please login."
          this.isLoginMode = true;
        }
      },
```

Navigate to _auth.component.html_, and include `msg`.

```html
<p class="alert alert-success mt-3" *ngIf="msg">{{ msg }}</p>
```

We Have a bug, when the user logs in, the user isn't able to navigate to bookshelf. Navigate to `auth.service.ts`, under `handleAuth`: we no longer need to multiply the expiration time by 1000 because it is already in MS. Also, because `this.autoSignOut(expiresIn \*1000) includes the added multiplication, setTimeout will break because it's too big of a number. Either way, we don't want to multiply by 1000 because it's in MS already.

```ts
    const expDate = new Date(new Date().getTime() + expiresIn);
    .
    .
    .
    this.autoSignOut(expiresIn);
```

<div id="auto-login-and-bugs"></div>

## Auto Login and Bugs

Now that we have sign up and logging in handled. Let's go ahead and allow the user to auto login. After logging in and refreshing the page, we are redirected to the auth page. Let's create a guard to redirect the user if the user user has a token stored in local storage.

Run `ng g shared/auth/session` and enter for `CanActivate`.

```ts
import { Injectable } from "@angular/core";
import {
  ActivatedRouteSnapshot,
  CanActivate,
  Router,
  RouterStateSnapshot,
  UrlTree,
} from "@angular/router";
import { map, take } from "rxjs/operators";
import { AuthService } from "./auth.service";

@Injectable({
  providedIn: "root",
})
export class SessionGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}
  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
    return this.authService.currUser.pipe(
      take(1),
      map((user) => {
        const userData = JSON.parse(localStorage.getItem("userData"));
        if (userData) {
          return this.router.createUrlTree(["bookshelf"]);
        } else {
          return true;
        }
      })
    );
  }
}
```

In the routing module, include SessionGuard under the `auth` path.

```ts
  {
    path: 'auth',
    canActivate: [SessionGuard],
```

Because we included the SessionGuard under auth, we must then alter the `bookshelf` path and it's guard. Right now the guard (auth guard) will return the user to `auth` when `user` is null. Which, will create an infinite loop.

Navigate to `auth.guard.ts`. Get the data from localstorage, if the userData exists as well as user, return true otherwise go back to the auth page.

```ts
map((user) => {
  const userData = JSON.parse(localStorage.getItem("userData"));
  if (userData || user) {
    this.authService.autoSignIn();
    return true;
  } else {
    return this.router.createUrlTree(["auth"]);
  }
});
```

When the user does exist, execute `autoSignIn` from `authService`. This will setup the user as well as the auto sign out.

```ts
        if (userData || user) {
        this.authService.autoSignIn();
          return true;
```

We can navigate to the `app.component.ts` file and get rid of the auto signin.

```ts
  ngOnInit() {
  }
```
<div id="authorizing-user-requests"></div>

## Authorizing User Requests

In order for the user to send any other requests to the api, they must be authenticated first! That means, we have to attach their token to each request after logging in.

Navigate to `shared/auth/auth-interceptor.service.ts`. Let's adjust the `modifiedReq` so that the token will be attached to each request.

```ts
const modifiedReq = req.clone({
  setHeaders: {
    Authorization: "Bearer " + user.token,
  },
});
```

We don't want every request to attach the token, specifically when we want requests from `openlibrary`.
```ts
      exhaustMap((user) => {
        // Make sure we have a user
        if (!user || req.url.includes('http://openlibrary.org/search.json?q=')) return next.handle(req);
```

Let's navigate to `auth.service.ts` and call `fetchBooks()` from `httpService`.

```ts
if (loadedUser.token) {
  this.currUser.next(loadedUser);
  const expDur =
    new Date(_tokenExpirationDate).getTime() - new Date().getTime();
  this.autoSignOut(expDur);
  this.httpService.fetchBooks().subscribe();
}
```

<div id="auto-fetching-books"></div>

## Auto Fetching books

It'd be nice for the user to automatically see his books upon signing in or refreshing the page. Before doing this, let's create a few books in the rails console for the user.

```
User.first.books.create(title: "It", author: "Stephen King", genre: "Horror", coverImagePath: "https://images-na.ssl-images-amazon.com/images/I/71tFhdcC0XL.jpg" )
User.first.books.create(title: "The Hobbit", author: "John Ronald Reuel Tolkien", genre: "Fantasy", coverImagePath: "https://wallpapercave.com/wp/OKiOlJg.jpg" )
```

Navigate to `book.model.ts`, include an `id` property.

```ts
    public isbn?: string,
    public id?: number
  ) {}
```

In _shared/auth/auth.service.ts_, inject `httpService`. In `handleAuth`, let's call a method we have yet to define called `fetchBooks` from `httpService`. Be sure to call `subscribe`.

```ts
this.currUser.next(formUser);
this.httpService.fetchBooks().subscribe();
```

Navigate to `shared/http/http.service.ts`. Let's redefine `fetchBooksFromFirebase` to `fetchBooks`. Instead of URL being firebase's url, change it to the correct url `my_books` endpoint from the book it api. Be sure to pass in the correct value of books from `res.payload`.

```ts
  fetchBooks() {
    return this.http.get("https://bookit-api-test-run.herokuapp.com/api/v1/books/my_books", {}).pipe(
      tap((res: any) => {
        this.bookshelfService.setBooks(res.payload);
      })
    );
  }
```

### Fixing errors

We get an error, navigate to `shared/navigation/navigation.html.ts`. Get rid of `onSaveData` as well as `onFetchData`. Then get rid of the settings dropdown as we no longer need it.

While we are at it let's include the following to show the user's email in the navbar.

```html
<li class="nav-item" style="color: grey" *ngIf="isAuthenticated">
  <a class="nav-link"> Welcome {{ user.email }}</a>
</li>
```

```ts
show = false;
user = null;
```

```ts
console.log("user", user);
this.user = user;
this.isAuthenticated = !!user;
```

The navigate to `bookshelf/book-resolver.service.ts`, replace `fetchBooksFromFirebase()` to `fetchBooks`.

```ts
  autoSignIn() {
    const userData: UserData = JSON.parse(localStorage.getItem('userData'));


    if (!userData) return;

    const { email, id, _token, _tokenExpirationDate } = userData;

    const loadedUser = new User(
      email,
      id,
      _token,
      new Date(_tokenExpirationDate)
    );

    this.router.navigate(['bookshelf']);

    this.currUser.next(loadedUser);
    const expDur =
      new Date(_tokenExpirationDate).getTime() - new Date().getTime();
    this.autoSignOut(expDur);
    this.httpService.fetchBooks().subscribe();
  }
```

```ts
    if (loadedUser.token) {
    this.router.navigate(['bookshelf']);
```

```ts
      this.signOut();
    }, +expDuration);
```

<div id="saving-a-book-from-bookshelf"></div>

## Saving a Book from Bookshelf

Let's navigate to `http.service.ts` and replace `saveBooksToFirebase` to `saveBook`. Adjust to to that of the api.

```ts
  saveBook(book) {
    this.http.post("https://bookit-api-test-run.herokuapp.com/api/v1/books", book).subscribe((res:any) => {
      this.bookshelfService.saveBook(res.payload.book)
    });
  }
```

Navigate to `bookshelf/bookshelf-editor.component.ts`, and call `saveBook` from `httpService`.

```ts
    } else {
      // Create a new book
      this.httpService.saveBook(this.bookDetails);
    }
```

Now you can save books to the database from your bookshelf editor.

<div id="saving-a-book-from-open-library"></div>

## Saving a Book from Open Library

Saving a book from provided by open library is actually quite simple. Navigate to `book-results.component.ts`. Under `onSaveBook`, call `savebook` from `httpService`.

```ts
  onSaveBook(book: Book) {
    book.genre = 'N/A';
    // Pass this to the "bookshelfService" "myBooks" array
    this.httpService.saveBook(book);
    // this.bookshelfService.saveBook(book);
  }
```

<div id="deleting-books"></div>

## Deleting Books

Navigate to `http.service.ts` and define `deleteBook`. Send a delete request to the APIs endpoint and include the id of the book.

```ts
  deleteBook(id) {
    return this.http.delete(`https://bookit-api-test-run.herokuapp.com/api/v1/books/${id}`)
  }
```

Navigate to `book-list.component.ts` and call `deleteBook` from `httpService`. Instead of getting the index, get the id. Remember, we need the id of the book for the delete request.

Inject HttpService.

```ts
  onRemoveBook(id:number) {
    this.httpService.deleteBook(id).subscribe();
    this.bookshelfService.removeBook(id);
  }
```

Let's also adjust `this.bookshelfService.removeBook(id)`. Navigate to `bookshelf.service.ts`, and then let's define a method called `getBookById(id)`.

```ts
  getBookById(id){
    let count = 0;
    let book = null;
    while(count < this.myBooks.length){

      if(this.myBooks[count].id == id){
        book = this.myBooks[count]
        break;
      }
      count++;
    }
    return book;
  }
```

```ts
  removeBook(id: number) {
      this.bookSelected.next(this.getBookById(id));
      this.myBooks = this.myBooks.filter((book:Book)=> book.id != id)
      this.bookListChanged.next(this.myBooks.slice());
  }
```

Let's Adjust `book-list.component.html`. Pass down the book id to the book component as well as to `onRemoveBook` as an argument. We no longer want to rely on the index to display information because it'll be redundant for us to navigate the id using the index. Also, our requests rely on ids rather than index.

```html
  <div
    class="col-md-12"
    *ngFor="let bookElement of myBooks | sortBooks: sortField"
  >
    <app-book [book]="bookElement" [id]="bookElement.id"></app-book>
    <button
      class="float-right"
      style="border: none; font-size: 16px"
      (click)="onRemoveBook(bookElement.id)"
    >
      &minus;
    </button>
  </div>
</div>
```

<div id="display-book-details"></div>

## Display Book Details

When display book details, it no longer works. We get `localhost:4200/bookshelf/undefined`. That's because in `book-list.component.html` we changed the name of the input from `idx` to `id`.

```html
<app-book [book]="bookElement" [id]="bookElement.id"></app-book>
```

Let's navigate to `book.component.ts` and change the property from `idx` to `id`

```ts
export class BookComponent implements OnInit {
  @Input() book: Book;
  @Input() id: number;

  constructor() {}

  ngOnInit(): void {}
}
```

Navigate to `book.component.html` and adjust the router link.

```html
<a
  style="cursor: pointer"
  class="list-group-item clearfix"
  [routerLink]="[id]"
  routerLinkActive="active"
></a>
```

We no longer get undefined, however, we still have to grab the book. Instead of using the index we are now using the id which makes it a lot easier to keep track of book records from the db.

Let's adjust `book-details.component.ts`. The file is configured to use the book index rather than the id.

Change the property `idx` to `id` and all of it's occurrences.

In `ngOnInit`, within the subject call back function, change `getBook` to `getBookById`.

```ts
export class BookDetailsComponent implements OnInit {
  book: Book;
  id: number;

  constructor(
    private route: ActivatedRoute,
    private router: Router,
    private bookshelfService: BookshelfService
  ) {}

  ngOnInit(): void {
    this.route.params.subscribe((params: Params) => {
      this.id = +params["id"];
      this.book = this.bookshelfService.getBookById(this.id);
    });
  }

  onEditBook() {
    this.router.navigate(["../", this.id, "edit"], { relativeTo: this.route });
  }

  onRemoveBook() {
    this.bookshelfService.removeBook(this.id);
  }
}
```

<div id="editing-books"></div>

## Editing Books

Great, let's test this out. After doing so, let's do the same for `bookshelf-editor.component.ts`. Navigate to `bookshelf-editor.component.ts`.

```ts
export class BookshelfEditorComponent implements OnInit {
  id: number;
  isEditMode = false;
```

```ts
 ngOnInit(): void {
    this.route.params.subscribe((params: Params) => {
      this.id = +params['id'];
      this.isEditMode = params['id'] != null;

      // If we are in "edit mode" => set the initial values for "bookDetails"
      if (this.isEditMode) {
        this.bookDetails = this.bookshelfService.getBook(this.id);
      }
    });
  }
```

Instead of `getBook`, use `getBookById`.

```ts
this.bookDetails = this.bookshelfService.getBookById(this.id);
```

### Updating Books from editor

In `bookshelf-editor.component.ts`, let's focus on updating the book. This logic happens in `onFormSubmit`. When `isEditMode` is true, the book will update. But if we have to do a couple things first.

Declare a variable called `updatedBook` and set that to `this.bookDetails`.

Set `updatedBook.id` to `this.id`. This is because the bookDetails does not have info about the id of the book it's editing in the form.

Call `updateBook` from httpService.We will define this after.

```ts
    if (this.isEditMode) {
      // Edit the book
      let updatedBook = this.bookDetails;
      updatedBook.id = this.id;
      let idx = this.bookshelfService.getBooks().findIndex(obj => obj.id == this.id)
      this.httpService.updateBook(updatedBook).subscribe();
      this.bookshelfService.updateBook(idx, this.bookDetails);
```

Navigate to `bookshelf.service.ts` and define `updateBook`.

```ts
  updateBook(book) {
    return this.http.put(`https://bookit-api-test-run.herokuapp.com/api/v1/books/${book.id}`, book)
  }
```

### Deleting Books from Editor

Inject HttpService.

```ts
  onRemoveBook() {
    this.httpService.deleteBook(this.id).subscribe();
    this.bookshelfService.removeBook(this.id);
    this.router.navigate(['../']);
  }
```


## Other not important Tasks

_bookshelf.component.ts_

```ts
const alertMsg = `Successful request`;
this.bookAlert(alertMsg);
```

<div id="deploying"></div>

## Deploying to Heroku

### STEP 1: Installing Developer Dependencies

_Terminal_:

- Install the latest version of the `@angular/cli@latest` and `@angular/compiler-cli` packages as developer dependencies.

- Install "express" (a package for create nodeJS servers) and "path" (for working with computer file-paths).

- Check your version of "node" and "npm" by running two commands. Save these values for later.

```zsh
# Only if you are not on version 12... version 13 will break this!
npm install --save-dev @angular/cli@latest @angular/compiler-cli
# . . .
npm install express path
# . . .
node -v
# . . .
npm -v
```

---

### STEP 2: Adding a Build Script && Engines

_package.json file_:

- Add a new script under the "build" script called "heroku-postbuild". Set it to run the code "ng build". Heroku will be looking for this script.

- Change the "start" script to run "node server.js"... a file we will create in the next step.

- Under the "devDependencies" object, add a new property "engines" that is set to an object containing the "node" and "npm" versions gathered earlier.

- _Note_: If the versions you have are throwing errors, paste these in: `"@angular/cli": "^12.2.1", "@angular/compiler-cli": "^12.1.5", "typescript": "~4.3.2"` and run `npm i`.

```json
{
  "name": "book-app",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "node server.js",
    "build": "ng build",
    "heroku-postbuild": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  },
  "private": true,
  "dependencies": {
    "@angular/animations": "~12.1.2",
    "@angular/common": "~12.1.2",
    "@angular/compiler": "~12.1.2",
    "@angular/core": "~12.1.2",
    "@angular/forms": "~12.1.2",
    "@angular/platform-browser": "~12.1.2",
    "@angular/platform-browser-dynamic": "~12.1.2",
    "@angular/router": "~12.1.2",
    "bootstrap": "^4.6.0",
    "express": "^4.17.1",
    "path": "^0.12.7",
    "rxjs": "~6.6.0",
    "tslib": "^2.2.0",
    "zone.js": "~0.11.4"
  },
  "devDependencies": {
    "@angular-devkit/build-angular": "~12.1.2",
    "@angular/cli": "^12.2.10",
    "@angular/compiler-cli": "~12.1.2",
    "@types/jasmine": "~3.8.0",
    "@types/node": "^12.11.1",
    "jasmine-core": "~3.8.0",
    "karma": "~6.3.0",
    "karma-chrome-launcher": "~3.1.0",
    "karma-coverage": "~2.0.3",
    "karma-jasmine": "~4.0.0",
    "karma-jasmine-html-reporter": "~1.7.0",
    "typescript": "~4.3.2"
  },
  "engines": {
    "node": "16.5.0",
    "npm": "7.20.0"
  }
}
```

### STEP 3: Creating the Express Server

_server.js file_:

- Inside the root of the application, create a new file called "server.js".

- This file should import the "express" and "path" packages, initialize `express()`, serve the static build files from the "dist" folder, route incoming server requests to the correct location, and listen on a new port.

- _Note_: To find out the projects name, run "ng build" in the terminal and check the file located directly under the "dist" directory.

```javascript
// Import Express & Path Packages
const express = require("express");
const path = require("path");

// Initialize express
const app = express();

// Serve static build files from the "dist" directory
app.use(express.static("./dist/PROJECTNAME"));

// Route incoming server requests to the correct files
app.get("/*", (req, res) =>
  res.sendFile("index.html", { root: "dist/PROJECTNAME/" })
);

// Start the app on the default Heroku port
app.listen(process.env.PORT || 8080);
```

- _Note_: Push the new code to GitHub!

---

### STEP 4 - Hooking Up Heroku

_Heroku Website_:

- In the browser, head on over to [https://www.heroku.com/](https://www.heroku.com/).

- Create an account.

- Create a new app.

- In the "Deployment Method" tab, choose the "Connect to GitHub" option.

- Select the repository the main project is located inside.

- Enable Automatic Deploys.

- Click Deploy Branch.

---

---

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!
