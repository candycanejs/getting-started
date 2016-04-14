# Custom Responses

Sometimes there we may not have valid data to go through our serializer.
In our action, we can break out of the standard response chain by calling `this.send` and passing in any data required.
Along with this, we can also use `this.setStatus` in our action to set the status code.

So, let's use these two methods to implement deleting records from our database using Bookshelf.
First we'll register our app to respond to `DELETE` request with a `lists/destroy` action:

```js
this.delete(`lists/:id`, `lists/destroy`);
```

Next, we'll setup our standard action to delete a record based on it's `id`:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const id = this.request.params.id;
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);

    return List.where({id}).fetch().then((list) => {
      if (list) {
        return list.destroy();
      }
    });
  }
}
```

Next, we can use the `after` hook to run something after our `data` hook has resolved its promise.
Here we'll set the status code to `204` and send an empty body.

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const id = this.request.params.id;
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);

    return List.where({id}).fetch().then((list) => {
      if (list) {
        return list.destroy();
      }
    });
  }

  after() {
    this.setStatus(204);
    this.send();
  }
}
```

Now, we can make a cURL request to delete our `list` #1.

```bash
curl -X DELETE -H "Content-Type: application/json" "http://localhost:3000/lists/1"
```

And we should get a blank response back from the server with a 204 error code.

[Next - Parsing JSON API Requests](./json-api-requests.md)
