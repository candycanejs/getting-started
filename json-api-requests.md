# Parsing JSON API Requests

To complete our REST API for the `lists` resource, we will need to do some work to parse incoming JSON API request data.

First, let's register a route for `POST` requests:

```js
this.post(`/lists`, `lists/create`);
```

And let's setup a basic action in `app/actions/lists/create.js`:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);
  }
}
```

Now we need to get the incoming title for our list from the request object.
Given the JSON API spec, the incoming response will look something like this:

```json
{
  "data": {
    "type": "lists",
    "attributes": {
      "title": "Groceries"
    }
  }
}
```

So, we can get the title for our new list with `this.response.body.attributes.title`.
Then we'll create a new instance of our list model and returned the saved result.

```js
data() {
  const bookshelf = this.app.make(`store`);
  const List = bookshelf.model(`list`);
  const title = this.request.body.data.attributes.title;

  const list = new List({title});
  return list.save();
}
```

We can test this with cURL:

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "data": {
    "type": "lists",
    "attributes": {
      "title": "Groceries"
    }
  }
}' "http://localhost:3000/lists"
```

And in response, we should get back:

```json
{"links":{"self":"http://localhost/lists"},"data":{"type":"lists","id":"7","attributes":{"title":"Groceries"},"links":{"self":"http://localhost/lists/7"}}}
```

## Updating Records

Now, to update a record, we can use the same technique in combination with the `set` and `save` methods on our Bookshelf model.
First we have to register our action (here we'll respond to both `PUT` and `PATCH` for legacy reasons):

```js
this.patch(`/lists/:id`, `lists/update`);
this.put(`/lists/:id`, `lists/update`);
```

And then we can create our action in `app/actions/lists/update`:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);

    const id = this.request.params.id;
    const title = this.request.body.data.attributes.title;

    return List.where({id}).fetch()
      .then((list) => {
        list.set({title});

        return list.save();
      });
  }
}
```

And we can make our cURL request again:

```bash
curl -X PUT -H "Content-Type: application/json" -d '{
  "data": {
    "type": "lists",
    "attributes": {
      "title": "Groceries"
    }
  }
}' "http://localhost:3000/lists/2"
```

And our expected result is something like:

```json
{"links":{"self":"http://localhost/lists"},"data":{"type":"lists","id":"2","attributes":{"title":"Groceries"},"links":{"self":"http://localhost/lists/2"}}}
```

[Next - Relational Data with Bookshelf](./relational-data.md)
