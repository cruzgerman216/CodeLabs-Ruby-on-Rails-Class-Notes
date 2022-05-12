# Book It Front End

---

### Table of Contents

- <a href="#getting-started">Getting Started</a>
- <a href="#signing-up">Sign Up</a>
- <a href="#signing-in">Sign In</a>
- <a href="#auto-login-and-bugs">Auto Login and Bugs</a>
- <a href="#authenticating-user-requests">Authenticating User Requests</a>
- <a href="#auto-fetching-books">Auto Fetching books</a>
- <a href="#logout">Logging out User</a>
- <a href="#saving-a-book-from-bookshelf">Saving a Book from Bookshelf</a>
- <a href="#saving-a-book-from-open-library">Saving a Book from Open Library</a>
- <a href="#deleting-books">Deleting Books</a>
- <a href="#Display-book-details">Display Book Details</a>
- <a href="#editing-books">Editing Books</a>
- <a href="#deploying">Deploying to Heroku</a>

---

## Videos 
-  <a href="https://youtu.be/COUOmu0uwso">Introduction</a>



<div id="getting-started"></div>

## Getting Started

Navigate to the [Angular repo](https://github.com/cruzgerman216/Front-End-Book-It-Application)

- Select `use this template`
- Follow the steps to create a repo
- Open in GitHub desktop 
- Open in vs code and run npm install
- Make sure you have the API running locally if not you can use the deployed API: https://paducah-bookit-api.herokuapp.com/ to follow along with.

<div id="signing-up"></div>

## Updating Sign Up

Navigate to `shared/auth/auth.service.ts` to configure the authentication setting. Remove all Firebase URLs/logic.

Change Urls to the local server Rails creates so we can test our Front End locally or you can use the deployed application. I will pick the deployed API.

```ts
const SIGN_UP_URL =
  "https://paducah-bookit-api.herokuapp.com/api/v1/users/create";
const SIGN_IN_URL =
  "https://paducah-bookit-api.herokuapp.com/api/v1/users/login";
```

Send a post request using the correct URL and remove the environment key as it's no longer needed. Also remove `returnSecureToken`. I went ahead and removed the pipe method and everything along with it.  In the future we can perhaps, send an email confirmed to make sure they own the email they are using.

```ts
  signUp(email: string, password: string) {
    return this.http
      .post<AuthResponseData>(SIGN_UP_URL, {
        email,
        password
      })
  }
```

Test to see if it works. Check the console for a successful response after signing up.

<div id="signing-in"></div>

## Signing In

In `shared/auth/auth.service.ts`, alter the `signIn` to the correct url and remove `returnSecureToken`. Be sure sure to include pipe. The `tap` operator will execute `handleAuth` which will authenticate the user.

```ts
  signIn(email: string, password: string) {
    return this.http
      .post<AuthResponseData>(SIGN_IN_URL, {
        email,
        password,
      })
      .pipe(
        tap((res) => {
          // Use "object destructuring" to get acess to all response values
          const { email, localId, idToken, expiresIn } = res;
          // Pass the response values into handleAuth method
          this.handleAuth(email, localId, idToken, +expiresIn);
        })
      );
  }
```

We are no longer dealing with the payload from FireBase, we are dealing with the payload from the Book It API so we have to go ahead and make a few changes.

Make `res` of type `any`.

Grab `expiry` and `value` from token in payload. Remember, when we send a request to sign in a user, our response body looks like this. 

(Retrieved response body from postman)
```json
{
    "success": true,
    "payload": {
        "user": {
            "id": 1,
            "email": "test@test.com",
            "first_name": null,
            "last_name": null,
            "name": " ",
            "token": {
                "id": 2,
                "created_at": "2022-05-10T18:50:06.828Z",
                "expiry": "2022-05-17T18:50:06.808Z",
                "ip": null,
                "revocation_date": null,
                "updated_at": "2022-05-10T18:50:06.828Z",
                "user_id": 1,
                "value": "44a8f3f0e3300df62a0d781dfba472b827505ba0"
            }
        },
        "token": {
            "id": 2,
            "created_at": "2022-05-10T18:50:06.828Z",
            "expiry": "2022-05-17T18:50:06.808Z",
            "ip": null,
            "revocation_date": null,
            "updated_at": "2022-05-10T18:50:06.828Z",
            "user_id": 1,
            "value": "44a8f3f0e3300df62a0d781dfba472b827505ba0"
        }
    }
}
```

Also get the `email` and `id` from `res.payload.user`.

```ts
      .pipe(
        tap((res: any) => {
          const { expiry, value } = res.payload.token;
          const { email, id } = res.payload.user;
          // Pass the response values into handleAuth method
          this.handleAuth(email, localId, idToken, +expiresIn);
        })
      );
```

Convert `expiry` into JS date object and get the difference using `Date.now()` in milliseconds. Store this in a variable called `expiresIn`. This will give us the expiration time of the token. 

```ts
      .pipe(
        tap((res: any) => {
          const { expiry, value } = res.payload.token;
          const { email, id } = res.payload.user;

          const expiresIn = new Date(expiry).getTime() - Date.now();
          this.handleAuth(email, id, value, +expiresIn);
        })
      );
```

Thus, 
```ts
  //   Sign In!
  signIn(email: string, password: string) {
    return this.http
      .post<AuthResponseData>(SIGN_IN_URL, {
        email,
        password,
      })
      .pipe(
        tap((res) => {
          const { expiry, value } = res.payload.token;
          const { email, id } = res.payload.user;

          const expiresIn = new Date(expiry).getTime() - Date.now();
          this.handleAuth(email, id, value, +expiresIn);
        })
      );
  }
```

Test the sign in logic. Check the console for a successful response after signing up.

### Messages and Navigating The User Upon Login

Adding messages allows the user to know what to do next. Navigate to `auth.component.ts`. Let's go ahead and define a new property called `msg` that is of type `string` and set to null.

```ts
errMsg: string = null;
msg: string = null;
```

In the `onAuthFormSubmit` method, within `authObsrv`'s subscribe method, check to see if `isLoginMode` is true, if so, navigate the user to bookshelf, otherwise set `msg` to `Thank you for signing up! Please login.`. Then set `isLoginMode` to `true`.

```ts
    this.authObsrv.subscribe(
      (res) => {
        console.log('AUTH RESPONSE SUCCESS:', res);
        if (this.errMsg) this.errMsg = null;

        if (this.isLoginMode) {
          this.router.navigate(['bookshelf']);
        }else {
          this.msg = "Thank you for signing up! Please login."
          this.isLoginMode = true;
        }
      },
```

Navigate to `auth.component.html` and include `msg`.

```html
    <!-- ERROR -->
    <p class="alert alert-danger mt-3" *ngIf="errMsg">{{ errMsg }}</p>
    <!-- MESSAGE -->
    <p class="alert alert-success mt-3" *ngIf="msg">{{ msg }}</p>
```

Go ahead and try signing up. Then login. After logging in, notice how the user did not navigate to the bookshelf.

We Have a bug, when the user logs in, the user isn't able to navigate to bookshelf. Navigate to `auth.service.ts`, under `handleAuth` method, we no longer need to multiply the expiration time by 1000 because it is already in MS. Also, because `this.autoSignOut(expiresIn *1000)` includes the added multiplication, setTimeout will break because it's too big of a number. Either way, we don't want to multiply by 1000 because it's in MS already. Update the following lines. 

```ts
    const expDate = new Date(new Date().getTime() + expiresIn);
    .
    .
    .
    this.autoSignOut(expiresIn);
```

Thus, 

```ts
  handleAuth(email: string, userId: string, token: string, expiresIn: number) {
    // Create Expiration Date for Token
    const expDate = new Date(new Date().getTime() + expiresIn);

    // Create a new user based on the info passed in . . . and emit that user
    const formUser = new User(email, userId, token, expDate);
    this.currUser.next(formUser);

    // Set a new timer for expiration token
    this.autoSignOut(expiresIn);

    // Save the new user to localStorage
    localStorage.setItem('userData', JSON.stringify(formUser));
  }
```

Now try to login. The user should be routed to the bookshelf path after logging in.

<div id="auto-login-and-bugs"></div>

## Auto Login and Bugs

Now that we have sign up and logging in handled, let's go ahead and allow the user to auto login. After logging in and refreshing the page, we are redirected to the auth view. Let's create a guard to redirect the user to the bookshelf view if the user has a token stored in local storage.

Run `ng g guard shared/auth/session` and enter for `CanActivate`.

We get 

```ts 
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, CanActivate, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class SessionGuard implements CanActivate {
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    return true;
  }
  
}
``` 

Inject `Router` into the class.


Within the can activate method, let's grab a hold of the user Data from localStorage. If there exists a value in localstorage, return a urlTree(basically navigate the user to the bookshelf view), otherwise return true. 

Thus, 

```ts
import { Injectable } from '@angular/core';
import {
  ActivatedRouteSnapshot,
  CanActivate,
  Router,
  RouterStateSnapshot,
  UrlTree,
} from '@angular/router';
import { map, take } from 'rxjs/operators';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root',
})
export class SessionGuard implements CanActivate {
  constructor( private router: Router) {}
  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
    const userData = JSON.parse(localStorage.getItem('userData'));
    // If session was stored, navigate user to bookshelf
    if ( userData) {
      return this.router.createUrlTree(['bookshelf']);
    } else {
      // If session not found, allow access to auth view
      return true;
    }
  }
}

```

Navigate to `app-routing.module`. Include `SessionGuard` under the `auth` path.

```ts
  {
    path: 'auth',
    canActivate: [SessionGuard],
    loadChildren: () =>
      import('./shared/auth/auth.module').then((m) => m.AuthModule),
  },
```

Because we included the `SessionGuard` under `auth`, we must then alter the `bookshelf` path and it's guard. Right now the guard (auth guard) will return the user to `auth` when `user` is null. Which, will create an infinite loop.

Example 

User who's sign in, refreshes the page on the bookshelf path. 
The auth guard hits the bookshelf and will return the user to the auth view when the user is null. Initially the user will always be null. 

Because of this logic, when the user navigates to the auth afterwords, the session guard will then navigate the user right back the bookshelf view because there is a session in local storage, and so on and so on in this infinite loop.

Navigate to `auth.guard.ts`. Get the data from localstorage, if the userData exists as well as user, return true otherwise go back to the auth page. Let's change this to the following.

```ts
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
    return this.authService.currUser.pipe(
      take(1),
      map((user) => {
        const userData = JSON.parse(localStorage.getItem("userData"));
        if (userData || user) {
          this.authService.autoSignIn();
          return true;
        } else {
          return this.router.createUrlTree(["auth"]);
        }
      })
    );
  }
}
```

Let's navigate to the `app.component.ts` file and get rid of the auto `signin`.

```ts
  ngOnInit() {
  }
```
<div id="authenticating-user-requests"></div>

## Authenticating User Requests

In order for the user to send any other requests to the api, they must be authenticated first! That means, we have to attach their token to each request after logging in.

Navigate to `shared/auth/auth-interceptor.service.ts`. In the `intercept` method, let's adjust the `modifiedReq` so that the token will be attached to each request. 

```ts
const modifiedReq = req.clone({
  setHeaders: {
    Authorization: "Bearer " + user.token,
  },
});
```

We don't want every request to attach the token, such as the requests we send to `openlibrary`.
```ts
      exhaustMap((user) => {
        // Make sure we have a user
        if (!user || req.url.includes('http://openlibrary.org/search.json?q=')) return next.handle(req);
```

Thus, 

```ts
  intercept(req: HttpRequest<any>, next: HttpHandler) {
    return this.authService.currUser.pipe(
      take(1),
      exhaustMap((user) => {
        // Make sure we have a user
        if (!user || req.url.includes('http://openlibrary.org/search.json?q=')) return next.handle(req);

        // Modify the reqest to attach the access token
        const modifiedReq = req.clone({
          setHeaders: {
            Authorization: "Bearer " + user.token,
          },
        });

        // Return the modified request
        return next.handle(modifiedReq);
      })
    );
  }
```

<div id="auto-fetching-books"></div>

## Auto Fetching books

Let's navigate to `auth.service.ts`. Inject `HttpService`. In the `autoSignIn` method call `fetchBooks()`, a method we will define in a moment. If the token does in fact exist, we want to call `fetchBooks`.

```ts
if (loadedUser.token) {
  this.currUser.next(loadedUser);
  const expDur =
    new Date(_tokenExpirationDate).getTime() - new Date().getTime();
  this.autoSignOut(expDur);
  this.httpService.fetchBooks().subscribe(); // <------------ Call fetchBooks() here
}
```

It'd be nice for the user to automatically see his books upon signing in or refreshing the page. Before doing this, let's create a few books in the Rails console

If you do have the book it app locally you can run this or you can do this on heroku in your very own app. If you are using the deployed link above, I wouldn't worry about creating a book, for now.

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

In `shared/auth/auth.service.ts`, inject `httpService`. In `handleAuth`, let's call a method we have yet to define called `fetchBooks` from `httpService`. Be sure to call `subscribe`.

```ts
this.currUser.next(formUser);
this.httpService.fetchBooks().subscribe();
```

Thus, 

```ts
  handleAuth(email: string, userId: string, token: string, expiresIn: number) {
    // Create Expiration Date for Token
    const expDate = new Date(new Date().getTime() + expiresIn);

    // Create a new user based on the info passed in . . . and emit that user
    const formUser = new User(email, userId, token, expDate);
    this.currUser.next(formUser);
    this.httpService.fetchBooks().subscribe();

    // Set a new timer for expiration token
    this.autoSignOut(expiresIn);

    // Save the new user to localStorage
    localStorage.setItem('userData', JSON.stringify(formUser));
  }
```

Navigate to `shared/http/http.service.ts`. Let's redefine `fetchBooksFromFirebase` to `fetchBooks`. Instead of URL being firebase's url, change it to the correct url `my_books` endpoint from the book it api. Be sure to pass in the correct value of books from `res.payload`.

```ts
  fetchBooks() {
    return this.http.get("https://paducah-bookit-api.herokuapp.com/api/v1/books/my_books", {}).pipe(
      tap((res: any) => {
        console.log("Fetching books", res)
        this.bookshelfService.setBooks(res.payload);
      })
    );
  }
```

### Fixing errors

We get an error, navigate to `shared/navigation/navigation.component.html`. Get rid of the settings dropdown in as we no longer need it.

While we are at it let's include the following to show the user's email in the navbar.

```html
<li class="nav-item" style="color: grey" *ngIf="isAuthenticated">
  <a class="nav-link"> Welcome {{ user.email }}</a>
</li>
```

Navigate to `navigation.component.ts`. Define `user`.
```ts
show = false;
user = null;
```

Within `ngOnInIt`, within the subscribe method, set the property user to the parameter user such as.

```ts
this.user = user;
this.isAuthenticated = !!user;
```

Get rid of methods `onFetchData` and `onSaveData` as we no longer need them.

Then navigate to `bookshelf/book-resolver.service.ts`, replace `fetchBooksFromFirebase()` to `fetchBooks`.

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

    if (loadedUser.token) {
      this.currUser.next(loadedUser);
      const expDur =
        new Date(_tokenExpirationDate).getTime() - new Date().getTime();
      this.autoSignOut(expDur);
      this.httpService.fetchBooks().subscribe();
    }
  }
```

<div id="logout"></div>

## Logging Out User

Logged out appears to work however, all we are doing is clearing localStorage. We aren't sending a request to the API to logout because when we do logout, that deletes the token for security reasons. 

Navigate to `auth.service.ts` and let's refactor `signOut` to the following. 

```ts
  // Sign Out!
  signOut() {
    this.http
      .delete('https://paducah-bookit-api.herokuapp.com/api/v1/users/logout')
      .subscribe((res: any) => {
        console.log('Logged out', res);
        if (res.success) {
          this.currUser.next(null);

          localStorage.removeItem('userData');

          if (this.tokenExpTimer) clearTimeout(this.tokenExpTimer);

          this.router.navigate(['auth']);
        }
      });
  }
```

<div id="saving-a-book-from-bookshelf"></div>

## Saving a Book from Bookshelf

Let's navigate to `http.service.ts` and replace `saveBooksToFirebase` to `saveBook`. Adjust to to that of the api.

```ts
  saveBook(book) {
    this.http.post("https://paducah-bookit-api.herokuapp.com/api/v1/books", book).subscribe((res:any) => {
      this.bookshelfService.saveBook(res.payload.book)
    });
  }
```

Navigate to `bookshelf/bookshelf-editor.component.ts`, and call `saveBook` from `httpService`. Be sure to inject `HttpService`.

```ts
    } else {
      // Create a new book
      this.httpService.saveBook(this.bookDetails);
    }
```

Now you can save books to the database from your bookshelf editor.
Test by adding a book. When we try to add a book we get `prompted` successfully removed however, that's false and it's something we need to change later. Refresh the page and see if the book you added is still there.

<div id="saving-a-book-from-open-library"></div>

## Saving a Book from Open Library Api

Saving a book provided by the open library api is actually quite simple. Navigate to `book-results.component.ts`. Under `onSaveBook`, call `saveBook` from `httpService`. Be sure to inject `HttpService`.

```ts
  onSaveBook(book: Book) {
    book.genre = 'N/A';
    // Pass this to the "bookshelfService" "myBooks" array
    this.httpService.saveBook(book);
    // this.bookshelfService.saveBook(book);
  }
```

Test it out.

<div id="deleting-books"></div>

## Deleting Books

Navigate to `http.service.ts` and define `deleteBook`. Send a delete request to the APIs endpoint and include the id of the book.

```ts
  deleteBook(id) {
    return this.http.delete(`https://paducah-bookit-api.herokuapp.com/api/v1/books/${id}`)
  }
```

Navigate to `book-list.component.ts` and call `deleteBook` from `httpService`. Be sure to inject `HttpService`. Instead of getting the index, get the id. Remember, we need the id of the book for the delete request. Change the parameter `idx` to `id`

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
Adjust removeBook. 

```ts
  removeBook(id: number) {
      this.bookSelected.next(this.getBookById(id));
      this.myBooks = this.myBooks.filter((book:Book)=> book.id != id)
      this.bookListChanged.next(this.myBooks.slice());
  }
```

Let's Adjust `book-list.component.html`. Pass down the book id to the book component as well as to `onRemoveBook` as an argument. We no longer want to rely on the index to display information because it'll be redundant for us to navigate using the index. Also, our requests rely on ids rather than index.

```html
<div class="row mb-3">
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

Test out the remove button.

<div id="display-book-details"></div>

## Display Book Details

When displaying book details, it no longer works. Try clicking on a book. We get `localhost:4200/bookshelf/undefined`. That's because in `book-list.component.html` we changed the name of the input from `idx` to `id`.

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
>
```

We no longer get undefined, however, choosing a book still appears to have a bug. Our book details component thinks we are getting the index and not the id. 

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

Test it out. 

<div id="editing-books"></div>

## Editing Books

Let's do the same for `bookshelf-editor.component.ts`. Navigate to `bookshelf-editor.component.ts`.

Replace `idx` property to `id` and all it's occurrences (replace all idx with id in this file).

```ts
export class BookshelfEditorComponent implements OnInit {
  id: number;
  isEditMode = false;
```

In `ngOInIt`, within the subscribe call back function, replace `getBook` with `getBookById`.
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

Test out whether you have the ability to view the book in edit mode.

### Updating Books from editor

In `bookshelf-editor.component.ts`, let's focus on updating the book. This logic happens in `onFormSubmit`. When `isEditMode` is true, the book will update. But if we have to do a couple things first.

Declare a variable called `updatedBook` and set that to `this.bookDetails`.

Set `updatedBook.id` to `this.id`. This is because the bookDetails does not have info about the id of the book it's editing in the form.

Call `updateBook` from httpService. We will define this after.

```ts
    if (this.isEditMode) {
      // Edit the book
      let updatedBook = this.bookDetails;
      updatedBook.id = this.id;
      let idx = this.bookshelfService.getBooks().findIndex(obj => obj.id == this.id)
      this.httpService.updateBook(updatedBook).subscribe();
      this.bookshelfService.updateBook(idx, this.bookDetails);
```

Navigate to `http.service.ts` and define `updateBook`.

```ts
  updateBook(book) {
    return this.http.put(`https://paducah-bookit-api.herokuapp.com/api/v1/books/${book.id}`, book)
  }
```

### Deleting Books from Details

Navigate to `book-details.component.ts` and inject HttpService.

```ts
  onRemoveBook() {
    this.httpService.deleteBook(this.id).subscribe();
    this.bookshelfService.removeBook(this.id);
    this.router.navigate(['../']);
  }
```


### Other

`bookshelf.component.ts`

```ts
const alertMsg = `Successful request`;
this.bookAlert(alertMsg);
```

<div id="deploying"></div>

## Deploying to Heroku

### STEP 1: Installing Developer Dependencies

_Terminal_:

- Install "express" (a package for create nodeJS servers)
- 
```zsh
# . . .
npm install express 
```

---

### STEP 2: Adding a Build Script && Engines

_package.json file_:

- Add a new script under the "build" script called "heroku-postbuild". Set it to run the code "ng build". Heroku will be looking for this script.

- Change the "start" script to run "node server.js"... a file we will create in the next step.

```json
  "scripts": {
    "ng": "ng",
    "start": "node server.js",
    "build": "ng build",
    "heroku-postbuild": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  },
```

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
  }
}
```

### STEP 3: Creating the Express Server

_server.js file_:

- Inside the root of the project folder(outside of the src folder), create a new file called "server.js".

- This file should import the "express" initialize `express()`, serve the static build files from the "dist" folder, route incoming server requests to the correct location, and listen on a new port.

- _Note_: To find out the projects name, run "ng build" in the terminal and check the file located directly under the "dist" directory.

```javascript
// Import Express & Path Packages
const express = require("express");

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
In the terminal, enter the follow

``` 
heroku create
```

``` 
heroku rename BookItApp
```

``` 
git push heroku main
```

``` 
heroku open
```

---

---

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!
