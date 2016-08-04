# REST Services for Typescript
This is a lightweight annotation-based [expressjs](http://expressjs.com/) extension for typescript.

It can be used to define your APIs using ES7 decorators.

**Table of Contents** 

- [REST Services for Typescript](#)
  - [Installation](#installation)
  - [Configuration](#configuration)
  - [Basic Usage](#basic-usage)
  - [@Path Decorator](#path-decorator)
    - [Path Parameters](#path-parameters)
  - [Http Methods](#http-methods)

## Installation

This library only works with typescript. Ensure it is installed:

```bash
npm install typescript -g
```

To install typescript-rest:

```bash
npm install typescript-rest --save
```

## Configuration

Typescript-rest requires the following TypeScript compilation options in your tsconfig.json file:

```typescript
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

## Basic Usage

```typescript
import * as express from "express";
import {Server, Path, GET, PathParam} from "typescript-rest";

@Path("/hello")
class HelloService {
  @Path(":name")
  @GET
  sayHello( @PathParam('name') name: string): string {
    return "Hello " + name;
  }
}

let app: express.Application = express();
Server.buildServices(app);

app.listen(3000, function() {
  console.log('Rest Server listening on port 3000!');
});

```

That's it. You can just call now:

```
GET http://localhost:3000/hello/joe
```

## @Path Decorator

The @Path decorator allow us to define a router path for a given endpoint.
Route paths, in combination with a request method, define the endpoints at 
which requests can be made. Route paths can be strings, string patterns, or regular expressions.

The characters ?, +, *, and () are subsets of their regular expression counterparts. 
The hyphen (-) and the dot (.) are interpreted literally by string-based paths.


*We use [path-to-regexp](https://www.npmjs.com/package/path-to-regexp) for matching the 
route paths; see the path-to-regexp documentation for all the possibilities in defining route paths.*


Some examples:

```typescript
@Path("/hello")
class HelloService {
}
```

```typescript
@Path("/test/hello")
class TestService {
}
```

This route path will match acd and abcd:

```typescript
@Path("ab?cd")
class TestService {
}
```

This route path will match abcd, abbcd, abbbcd, and so on:

```typescript
@Path("ab+cd")
class TestService {
}
```

This route path will match abcd, abxcd, abRANDOMcd, ab123cd, and so on:

```typescript
@Path("ab*cd")
class TestService {
}
```

This route path will match /abe and /abcde:

```typescript
@Path("/ab(cd)?e")
class TestService {
}
```

This route path will match butterfly and dragonfly, but not butterflyman, dragonfly man, and so on:

```typescript
@Path("/.*fly$/")
class TestService {
}
```
### Path Parameters

Route parameters are named URL segments that are used to capture the values specified at their position in the URL. 
The captured values are populated in the req.params object, with the name of the route parameter specified in 
the path as their respective keys. They can be refered through @PathParam decorator on a service method argument.

Some examples:

```typescript
@Path("/users")
class UserService {
   @Path("/:userId/books/:bookId")
   getUserBook(@PathParam("userId") userId: number, @PathParam("bookId") bookId: number): Promise<Book> {
      //...
   }
}
```
The requested URL http://localhost:3000/users/34/books/8989 would map the parameters as:

```
   userId: "34"
   bookId: "8989"
```

Since the hyphen (-) and the dot (.) are interpreted literally, they can be used along with route 
parameters for useful purposes.

```
Route path: /flights/:from-:to
Request URL: http://localhost:3000/flights/LAX-SFO
req.params: { "from": "LAX", "to": "SFO" }
```

```
Route path: /plantae/:genus.:species
Request URL: http://localhost:3000/plantae/Prunus.persica
req.params: { "genus": "Prunus", "species": "persica" }
```

## Http Methods