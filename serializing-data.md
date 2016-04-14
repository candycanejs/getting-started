# Serializing Data

Once data is fetched, it will need to be formatted for better use with client-side frameworks.
In Candycane.js the convention is to use ES2016 decorators to add serialization support for actions.

For this application, we will format our data to match the [JSON API Standard](http://jsonapi.org/).
To help with this, the standard project we've created comes with `candycane-jsonapi-mapper` which provides a decorator for using the [jsonapi-mapper](https://github.com/scoutforpets/jsonapi-mapper) node package to serialize Bookshelf models.

Let's look at using this for our `list/index` action.
First, we will import the `map` decorator from `candycane-jsonapi-mapper`, then we will call the decorator function passing in the name of our base resource (in this case `list`):

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);

    return List.fetchAll();
  }
}
```

Now, we can use cURL to see the results of our serialized data:

```bash
curl -X GET -H "Content-Type: application/json" "http://localhost:3000/lists"
```

Now our resulting data will be formatted and serialized to the JSON API spec:

```json
{
  "links": {
    "self": "http://localhost/lists"
  },
  "data": [
    {
      "type": "lists",
      "id": "1",
      "attributes": {
        "title": "New Years Resolution"
      },
      "links": {
        "self": "http://localhost/lists/1"
      }
    },
    {
      "type": "lists",
      "id": "2",
      "attributes": {
        "title": "New Years Resolution"
      },
      "links": {
        "self": "http://localhost/lists/2"
      }
    },
    {
      "type": "lists",
      "id": "3",
      "attributes": {
        "title": "New Years Resolution"
      },
      "links": {
        "self": "http://localhost/lists/3"
      }
    }
  ]
}
```

[Next - Dynamic Route Segments](./dynamic-routes.md)
