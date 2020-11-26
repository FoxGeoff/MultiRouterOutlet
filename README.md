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

## Task: 7.4.1 Creating links in templates with routerLink

1. Add ```<a class="nav-link" routerLink="/forums"><span class="nav-text">Forums</span></a>```

```text
In this use of routerLink, it accepts the string with the appropriate path to use. If the path starts with a forward slash, it will treat the URL as an absolute path from the domain. It could also be a relative path without the slash. This is probably the most common way to use routerLink.

When you use href with links, the browser will request a new URL from the server, which isn’t what we want.

That’s all it takes to create a link, but you can also bind an expression with routerLink for more dynamic links. To demonstrate this, we’ll add a link from the forums list to load the individual forum page. Open src/forums/forums/forums.component.html (yes, I know the file path is a bit redundant) and update the table row with the NgFor to include a routerLink to link to the forum:
```

1. Use: ```<tr *ngFor="let forum of forums" [routerLink]="[forum.alias]">```

```Text
You’ll notice in this case we’re binding a value to it (by wrapping it with the [] notation). When you bind a value, it expects an array of path segments that it will use to construct the complete URL. In this case, we’re setting the forum alias value in this array. By default, it will treat routes as relative to the current URL, which means it will append to the current route. On this page, the URL is /forums, and each link will be routed to the forum by alias, like /forums/1-announcements.

Now we need to get the route parameter information into our Forum component so it knows which forum to display. Often, you’ll use this route parameter information to call one of your services to load data, and we’ll do that here.

Angular provides a service that contains the currently active route metadata. It gives you access to a wealth of information, described at https://angular.io/api/router/ActivatedRoute. It contains details such as the current URL, query or router parameters (and their current values), information about any children or parent routes, and more.

Using this service, we’ll access the current parameter information. Open src/app/forums/forum/forum.component.ts and update the class as you see in the following listing. It will inject new services and handle getting access to the route parameters.

```

```TypeSCript
export class ForumComponent implements OnInit {
  forum: Forum;

  constructor(
    private route: ActivatedRoute,      // A
private router: Router,                 // A
    private forumsService: ForumsService) { }

  ngOnInit() {
    this.route.params.subscribe((params: Params) => {             // B
this.forum = this.forumsService.forum(params['forum_alias']);     // C
if (!this.forum) this.router.navigate(['/not-found']);            // D
});
  }
}
```

```Text

This component is activated when the user navigates to a page like /forums/1-announcements. We start off by injecting the active route into the route property, and the router service into the router property. You’d normally need to import them into the file at the top, but that should already be done for you.

Inside of OnInit, we then create a subscription to listen for when the parameters change. Many of the values returned by ActivatedRoute are exposed as observables, so you’ll have to subscribe to get the values. The primary reason why is that when you have nested routes where a parent and child route are active at the same time, a parent component can subscribe to get updates as any child routes are loaded.

The params observable you subscribe to will return a Params object type, which allows you to access the properties like params['forum_alias']. We then use our service to get the requested forum by passing in the forum alias from the params, and set that to the forum property.

Just because a parameter is provided to a component doesn’t guarantee that the alias provided is a valid one, which is a case we should handle. Therefore, if the forum doesn’t exist, we’ll redirect the user to a not found page.

Throughout the rest of the chapter we’ll write this same basic active route observable anytime we need access to the parameters. There are other ways to access the current parameters on the page, but they have some weaknesses and aren’t typically used or recommended. One example is being able to get the values one time using a promise instead of an observable, but the drawback there is that you’d only get the parameters one time, and the component wouldn’t know about new parameters if the route changed without the component being recreated.

If you’re familiar with observables, you may have noticed we didn’t unsubscribe from the params observable, because an observable typically persists after a component is destroyed, as an event listener can do. Typically, you should manually unsubscribe or you’ll end up with memory leaks, but in this case it’s allowed because Angular will destroy this observable as soon as the active route is no longer active. You could unsubscribe in the component’s OnDestroy hook if you prefer.

At this point, if you go to http://localhost:4200/forums, you should be able to click a forum and see the title of that forum appear in a new page with the URL changing. Try a few forums to make sure the values change depending on the forum you view.

Now we need to see the threads in a single forum, and we’ll use child routes to help us define these routes that are organizationally linked to the specific forum.
```

## Task: 7.5 Child routes

## Task: 7.6 Secondary routes
