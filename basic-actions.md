# Basic Actions

In Candycane, we have to register "Actions" with our router to respond to HTTP requests.
To understand more about this, let's create a basic action that responds to a `POST` request and returns the JSON sent in wrapped in an object.

So a `POST` request with the data:

```json
{
  "firstName": "Homer",
  "lastName": "Simpson"
}
```

Would return:

```json
{
  "data": {
    "firstName": "Homer",
    "lastName": "Simpson"
  }
}
```

## Registering Actions With the Router

To respond to a `POST` request to `info`, we will need to modify the `app/router.js` file to register a new route.

The `registerRoutes` function is called in the application start up process and will register routes for our application and load in "Actions" from our `app/actions` directory.
So, let's register a new route that will respond to `POST` requests to the `/info` url, and we will use the `info` action to handle this request:

```js
registerRoutes() {
  this.get(`/`, `version`);

  this.post(`/info`, `info`);
}
```

If we look in our terminal where we are running `nodemon dist/bin/www`, we will see an error that the module `dist/actions/info` could not be found.
To fix this, we will need to define a new "Action" class in `app/actions/info.js`:

```js
import Action from 'candycane/dist/http/action';

export default class extends Action {

}
```

This is the basic structure for defining any action with Candycane.
We are extending from the base `Action` class that Candycane provides.

Now, we can create a `data` method within our class which will load and return data to respond to our HTTP request:

```js
export default class extends Action {
  data() {
    return {
      x: `my data`,
    };
  }
}
```

Now that we have our action registered and a `data` method defined, let's make a post request using CURL:

```bash
curl -X POST "http://localhost:3000/info"
```

We should get the response:

```json
{"x":"my data"}
```

## Retrieving Input

Now that we have a way to return JSON data, let's look at retrieving data and working with it.

In Candycane actions, the HTTP request object is available as `this.request`.
Since the request is parsing our input JSON using [body-parser](https://github.com/expressjs/body-parser), we can use `this.request.body` to pull in JSON data from the request.
Let's now use this to wrap the incoming JSON request data with an parameter named `request-data`:

```js
export default class extends Action {
  data() {
    return {
      'request-data': this.request.body,
    };
  }
}
```

And let's make a new CURL request sending our Homer Simpson information from before:

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "firstName": "Homer",
  "lastName": "Simpson"
}' "http://localhost:3000/info"
```

Our server should respond with:

```json
{"request-data":{"firstName":"Homer","lastName":"Simpson"}}
```

[Next - Working with Bookshelf Models](./bookshelf.md)
