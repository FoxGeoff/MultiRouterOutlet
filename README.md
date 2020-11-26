# Community

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 1.3.2.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Task: 7.2 Route definitions and router setup

1. Ref: <https://livebook.manning.com/book/angular-in-action/chapter-7/31>
2. run ```npm install --save @angular/router```
3. Description  of routing properties and methods: <https://angular.io/guide/router#configuration>

```text
url           | An Observable of the route path(s), represented as an array of strings for each part of the route path.
data          | An Observable that contains the data object provided for the route. Also contains any resolved values from the resolve guard.
paramMap      | An Observable that contains a map of the required and optional parameters specific to the route. The map supports retrieving single and multiple values from the same parameter.
queryParamMap | An Observable that contains a map of the query parameters available to all routes. The map supports retrieving single and multiple values from the query parameter.
fragment      | An Observable of the URL fragment available to all routes.
outlet        | The name of the RouterOutlet used to render the route. For an unnamed outlet, the outlet name is primary.
routeConfig   | The route configuration used for the route that contains the origin path.
parent        | The route's parent ActivatedRoute when this route is a child route.
firstChild    | Contains the first ActivatedRoute in the list of this route's child routes.
children      | Contains all the child routes activated under the current route.
```

```typescript
/**
 * flie: app.module.ts
 */

...
const appRoutes: Routes = [
  { path: 'login', component: LoginComponent },
  { path: '', redirectTo: '/forums', pathMatch: 'full' },
  { path: '**', component: NotFoundComponent },
];
...
  imports: [
    BrowserModule,
    FormsModule,
    ClarityModule.forRoot(),
    ForumsModule,
    RouterModule.forRoot(appRoutes)
  ],
...
```

```text
What we’ve done is include the router in our application, and we’ve used the forRoot() method
to declare that we’re using the router in the main App module. It also takes a parameter,
which should be an array of routes declared by this module. If you don’t pass anything here,
the router won’t know about any of your routes, so it’s the way to pass configuration into the router.
```

## Task: 7.3 Feature modules and routing

1. Ref: <<https://livebook.manning.com/book/angular-in-action/chapter-7/61>
2. src/app/forums/forums.module.ts file and import the Router module:
3. Add:```import { RouterModule, Routes } from '@angular/router';```

```TypeScript
/**
 * file: forums.module.ts
 */
...
import { RouterModule, Routes } from '@angular/router';
...
const forumsRoutes: Routes = [
  { path: 'forums', component: ForumsComponent }
];
...
imports: [
  BrowserModule,
  FormsModule,
  ClarityModule.forChild(),
 RouterModule.forChild(forumsRoutes),
],

```

```text
Here we use forChild() instead of forRoot(), because we’re declaring routes that belong
to an imported module instead of the main App module. Otherwise, the process is the same,
and the route definitions follow the same rules.
```

## Task: 7.4 Route parameters

1. <https://livebook.manning.com/book/angular-in-action/chapter-7/76>
2. Example Path: ```/forums/1-announcement```
3. Add route ```{ path: 'forums/:forum_alias', component: ForumComponent }```
