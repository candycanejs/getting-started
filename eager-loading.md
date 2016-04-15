# Sideloading and Eager Loading Models

Now that we have `reminders` that belong to our `list` resources, our API should return these relationships and sideload them as "included" resources for our JSON API endpoints.

To do this we can quickly modify our existing `lists/index` and `lists/show` actions.
Here we will use the `withRelated` option for Bookshelf `fetchAll` and `fetch` to eager load our relationships.
This will load in our related models as efficiently as possible and the `jsonapi-mapper` can handle the serialization of these sideloaded relationships.

To start we'll update the `data` hook in our `lists/index`:

```js
data() {
  const bookshelf = this.app.make(`store`);
  const List = bookshelf.model(`list`);

  return List.fetchAll({withRelated: `reminders`});
}
```

Then we can update the `data` hook in `lists/show`:

```js
data() {
  const id = this.request.params.id;
  const bookshelf = this.app.make(`store`);
  const List = bookshelf.model(`list`);

  return List.where({id}).fetch({withRelated: `reminders`});
}
```

And finally, we can update `lists/update`:

```js
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
```

To see the results of this work, let's make a cURL request to `/lists`:

```bash
curl -X GET -H "Content-Type: application/json" "http://localhost:3000/lists"
```

And now we have a huge response with our sideloaded data:

```json
{
  "links": {
    "self": "http://localhost/lists"
  },
  "data": [
    {
      "type": "lists",
      "id": "2",
      "attributes": {
        "title": "Groceries"
      },
      "links": {
        "self": "http://localhost/lists/2"
      },
      "relationships": {
        "reminders": {
          "data": [
            {
              "type": "reminders",
              "id": "2"
            }
          ],
          "links": {
            "self": "http://localhost/lists/2/relationships/reminders",
            "related": "http://localhost/lists/2/reminders"
          }
        }
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
      },
      "relationships": {
        "reminders": {
          "data": [],
          "links": {
            "self": "http://localhost/lists/3/relationships/reminders",
            "related": "http://localhost/lists/3/reminders"
          }
        }
      }
    }
  ],
  "included": [
    {
      "type": "reminders",
      "id": "2",
      "attributes": {
        "done": "false",
        "name": "Water Plants"
      },
      "links": {
        "self": "http://localhost/lists/2"
      }
    }
  ]
}
```

[Next - Not Found Errors](./not-found.md)
