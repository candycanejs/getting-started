# Dynamic Route Segments

Now that we have our index route setup for our JSON API resource, we need to also have a route for looking up a single `list` item by a unique id.
This means that we will need `/lists/1` to show only the data for the `list` resource with the `id` `1`.

Since Candycane uses Express.js to register it's routes, the syntax for dynamic route segments is the same as using Express.
To create a dynamic segment, precede the segment with `:`.

Let's register a `show` action for our `lists` resource in `app/router.js`:

```js
this.get(`/lists/:id`, `lists/show`);
```

Then we can create our base action in `app/actions/lists/show.js`, and we will set up JSON API serialization:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {

  }
}
```

Now we need a way to get the `id` parameter from our request object.
Luckily, we are in an action so we can use: `this.request.params.id`.
And then, we'll look up our model from bookshelf and return the results from `fetch`:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const id = this.request.params.id;
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);

    return List.where({id}).fetch();
  }
}
```

Now, we can use cURL to see our results:

```bash
curl -X GET -H "Content-Type: application/json" "http://localhost:3000/lists/1"
```

And our resulting data:

```json
{
  "links": {
    "self": "http://localhost/lists"
  },
  "data": {
    "type": "lists",
    "id": "1",
    "attributes": {
      "title": "New Years Resolution"
    },
    "links": {
      "self": "http://localhost/lists/1"
    }
  }
}
```

[Next - Custom Responses](./custom-responses.md)
