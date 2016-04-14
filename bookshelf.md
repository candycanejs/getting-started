# Working With Bookshelf Models

While working with raw database queries can be incredibly powerful, for productivity and organization, most applications use an ORM.
Just like the KNEX addon, Candycane CLI automatically added `candycane-bookshelf` to our application which integrates the popular [Bookshelf.js](bookshelfjs.org) ORM into the Candycane application.

## Creating Bookshelf

During the boot process, the `candycane-bookshelf` addon automatically registers all modules within the `app/models` directory as models in the Bookshelf registry.
This means that we can create an `app/models/list.js` file to create a `list` model in Bookshelf.
Here, we'll make this model connect to our `lists` table:

```js
export default {
  tableName: `lists`,
}
```

## Using Bookshelf From Actions

To get information from our Bookshelf collection, let's start by registering a new route in our `router.js`:

```js
this.get(`/lists`, 'lists/index');
```

And then, we will need to create an action in `app/actions/lists/index.js`:

```js
import Action from 'candycane/dist/http/action';

export default class extends Action {
  data() {

  }
}
```

Just like the `db` instance in our app container, `candycane-bookshelf` registers a `store` with the app container.
So, we can get Bookshelf with ``this.app.make(`store`)``.
Once we have Bookshelf, we can look up a model from the registry and run `fetchAll`:

```js
data() {
  const bookshelf = this.app.make(`store`);
  const List = bookshelf.model(`list`);

  return List.fetchAll();
}
```

Now, let's test this out by making a request with cURL from our terminal:

```bash
curl -X GET -H "Content-Type: application/json" "http://localhost:3000/lists"
```

We should get a result that looks something like:

```json
[{"id":1,"title":"New Years Resolution"},{"id":2,"title":"New Years Resolution"},{"id":3,"title":"New Years Resolution"},{"id":4,"title":"New Years Resolution"}]
```

[Next - Serializing Data](./serializing-data.md)
