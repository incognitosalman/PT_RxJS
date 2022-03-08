# PT_RXJS

# Calling an API & Manipulating Its Data using RxJS Operators
> [RxJS](https://rxjs.dev/guide/overview) is a javascript library that brings the concept of "reactive programming" to the web.

Reactive programming is just a different way of building software applications. Essentially, your software is built to "react" to changes that happen (like click events, data being fetched, etc.)

## Basic Concepts of RxJS
> An [Observable](https://rxjs.dev/guide/observable) is basically a function that can return a stream of data to an observer over time.

> Observer is there to execute some code whenever it receives a new value from the observable. We connect observable to the observer through subscription using a method called subscribe.

> So, observer can implement (in subscribe method) upto three methods next, error, and complete.

- next() will be called by the observable whenever it emmits a new value.
- error() will be called whenver observable throws an error. 
- complete() is called whenver the observabe is done.
![Observable](https://user-images.githubusercontent.com/100778209/156986684-c299d262-05d9-4dc8-b737-45d828236b99.png)

```

var observer = {
	next: function(value) {
  	console.log(value);
  },
  error: function(error) {
  	console.log(error);
  },
  complete: function() {
  	console.log('Completed');
  }
};

var subscription = Rx.Observable.create(function(obs) {
	  obs.next('A value');
  //obs.error('Error');
   obs.complete();
})
	.subscribe(observer);
  
```
## Lets implement it in our Angular Project
we are going to call an existing api using [JSON placehoder](https://jsonplaceholder.typicode.com/) which will return data about Blogs as an observable. Then we will manipulate that data using RxJS operators. Here we will show the final result using subscription.

### Step 1: Scaffold a new angular application
In order to scaffold an angular application from scratch we have another lab which is pre-requisite for this lab. Here you can access the complete [details](PatternsTechGit/PT_RxJS).

### Step 2: Inject HttpClient in angular
 Angular provides a client HTTP API for Angular applications, the **HttpClient** service class in **@angular/common/http**. So we need to import this into our app.module.ts file.
`import { HttpClientModule } from '@angular/common/http';`

Moreover, in order to make it available to our **AppModule** we need to import it into list of dependencies.

```

@NgModule({
  imports: [
    BrowserModule,
    // import HttpClientModule after BrowserModule.
    HttpClientModule,
  ],
  declarations: [
    AppComponent,
  ],
  bootstrap: [ AppComponent ]
})

```

### Step 3: Using environment file
Now we need to access the [API](https://jsonplaceholder.typicode.com/posts) in our project. So, we will be saving the base url of the API **https://jsonplaceholder.typicode.com/** into environment file (environment.ts and environment.prod.ts).

```

export const environment = {
  production: false,
  baseUrl: 'https://jsonplaceholder.typicode.com/'
};

```

### Step 4: Creating a type safe model
Since we are using typescript in angular project. In order to achieve the type safety in our call to API, fetching and storing data into objects we are creating a type safe model in models folder. 

- Create new folder **models** in app folder 
- Create a new file **post.ts** in there.
- Write the following code in this file

```

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

```

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
```
constructor(private httpClient: HttpClient) { }
```
-  Declare and initialize the **baseUrl** from environment
```
baseUrl = environment.baseUrl;
```
-  Create a method **getBlogPosts**
```
getBlogPosts(): Observable<Blog[]> {
    return this.httpClient.get<Blog[]>(this.baseUrl + 'posts');
  }
```

### Step 7: Using service in angular component
In AppComonent we will inject **Blog Service** and on its **ngOnInit** we will call **getBlogPosts** and we will manupulate return observable using RxJS operators.

- Implement the OnInit life cycle hook
```
export class AppComponent implements OnInit{	
  ngOnInit(): void {
  }
}
```
- Inject the **Blog Service**
```
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
```
blogs: Blog[] = [];
```
- Create the method getTop10BlogPosts
```
getTop10Blogs(): void {
    this.blogService
      .getBlogPosts()
      .subscribe(response => { this.blogs = response; });
  }
```


### Step 7: Show result in completed function of the Observer
- import RxJS operators
```
import { map } from 'rxjs/operators';
```
- Apply the operators
```
getTop10Blogs(): void {
    this.blogService
      .getBlogPosts()
      .pipe(map(p => p.filter(x => x.id < 10)))
      .subscribe(response => { this.blogs = response; });
  }
```

