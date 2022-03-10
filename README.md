# PT_RXJS

# Calling an API & Manipulating Its Data using RxJS Operators
> [RxJS](https://rxjs.dev/guide/overview) is a javascript library that brings the concept of "reactive programming" to the web.

Reactive programming is just a different way of building software applications. Essentially, your software is built to "react" to changes that happen (like click events, data being fetched, etc.)

## Basic Concepts of RxJS
> An [Observable](https://rxjs.dev/guide/observable) is basically a function that can return a stream of data to an observer over time.

> [Observer](https://rxjs.dev/guide/observer) is there to execute some code whenever it receives a new value from the observable. We connect observable to the observer through subscription using a method called subscribe.

> So, observer can implement (in subscribe method) upto three methods next, error, and complete.

- `next()` will be called by the observable whenever it emmits a new value.
- `error()` will be called whenver observable throws an error. 
- `complete()` is called whenver the observabe is done.
![Observable](https://user-images.githubusercontent.com/100778209/157576466-819f0b12-8bac-401d-a0e1-c43485eb2f96.png)

```typescript

var observer = {
  next: function (value) {
    console.log(value);
  },
  error: function (error) {
    console.log(error);
  },
  complete: function () {
    console.log('Completed');
  }
};

var subscription = Rx.Observable.create(function (obs) {
  obs.next('A value');
  //obs.error('Error');
  obs.complete();
})
  .subscribe(observer);  
```
## Lets implement it in our Angular Project
we are going to call an existing api using [JSON placehoder](https://jsonplaceholder.typicode.com/) which will return data about Blogs as an observable. 

We will manipulate that data using RxJS operators. Here we will show the final result using subscription.

### Step 1: Scaffolding a new angular application
To scaffold a new angular application. See:

### Step 2: Incorporating Http Client to Communicate with API
To use HttpClient service in angular. See:

### Step 3: Setting up API's Base Url as Environment Variable
To setup Base Url as environment variable in development environment we will simple put it in environment.ts file. 

```typescript

export const environment = {
  production: false,
  baseUrl: 'https://jsonplaceholder.typicode.com/'
};

```
To understand more about environment configuration. See 9configuring application environment](https://angular.io/guide/build)

### Step 4: Creating a type safe model
API has a function `/posts` that returns a array of object that looks like.

![image](https://user-images.githubusercontent.com/100778209/157579376-36ad32e3-d21a-4b4e-85bb-d4b191cdefdd.png)

To capture this array in type safe manner we will introduce a TypeScript interface matching the same property names as of returned objects.

We will create a folde called `models` to contain all of our models. In it we will create a file called `posts.ts`

```typescript

export interface Blog {
  userId: number;
  id: number;
  title: string;
  body: string;
}

```

### Step 5: Creating a new service
We will be creating a service **BlogService** to fetch data from the [API](https://jsonplaceholder.typicode.com/posts).

- Create a new folder **services** in the app folder
- Execute the command for creation of new service **BlogService** with in the **services** folder

```
ng generate service blog
```

- It will generate the file blog.service.ts with following code 

```typescript

import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class BlogService {

  constructor() { }
}


```

### Step 6: Injecting service as dependency and fetch data from API
Now we will use the HttpClient in our service to get data from the API. 
- Inject the HttpClient in BlogService
```typescript
constructor(private httpClient: HttpClient) { }
```
-  Declare and initialize the **baseUrl** from environment
```typescript
baseUrl = environment.baseUrl;
```
-  Create a method **getBlogPosts**
```typescript
getBlogPosts(): Observable<Blog[]> {
    return this.httpClient.get<Blog[]>(this.baseUrl + 'posts');
  }
```

### Step 7: Using service in angular component
In AppComonent we will inject **Blog Service** and on its **ngOnInit** we will call **getBlogPosts** and we will manupulate return observable using RxJS operators.

- Implement the OnInit life cycle hook
```typescript
export class AppComponent implements OnInit{	
  ngOnInit(): void {
  }
}
```
- Inject the **Blog Service**
```typescript
constructor(private blogService: BlogService) {
  }
```

> An operator is a pure function which takes in observable as input and the output is also an observable.

#### Pipe
It’s a standalone function and a method on the Observable interface that can be used to combine multiple RxJS operators to compose asynchronous operations.

#### Map
Applies a given function to each value emitted by the source Observable, and emits the resulting values as an Observable.

#### Filter
Filter items emitted by the source Observable by only emitting those that satisfy a specified condition.

### Step 6: Filter output of map (filter top 10)
- Declare and initialize an empty **Blog** array
```typescript
blogs: Blog[] = [];
```
- Create the method getTop10BlogPosts
```typescript
getTop10Blogs(): void {
    this.blogService
      .getBlogPosts()
      .subscribe(response => { this.blogs = response; });
  }
```


### Step 7: Show result in completed function of the Observer
- import RxJS operators
```typescript
import { map } from 'rxjs/operators';
```
- Apply the operators
```typescript
getTop10Blogs(): void {
    this.blogService
      .getBlogPosts()
      .pipe(map(p => p.filter(x => x.id < 10)))
      .subscribe(response => { this.blogs = response; });
  }
```

