# Creating Relational Data with Bookshelf

So far, we've created a full RESTful API for our `lists` resource and formatted it to match the JSON API spec.

But, for our application, we will need reminders that belong to various lists.
Luckily, Bookshelf.js makes these types of data relations easy.

## Defining Relationships with Bookshelf

To start, let's update our `list` model to include a relationship that says it should have many reminders:

```js
export default {
  tableName: `lists`,

  reminders() {
    this.hasMany(`reminder`);
  }
}
```

Next, we'll need to create a new model for our reminders and we'll define their parent `list` relationship as `belongsTo`

```js
export default {
  tableName: `reminders`,

  list() {
    this.belongsTo(`list`);
  }
}
```

## Storing New Reminders

Now that we have our models set up, we can create an action to store new reminders.
Let's register this in our router:

```js
this.post(`/reminders`, `reminders/create`);
```

Then we can set up our action:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`reminder`)
export default class extends Action {
  data() {
    const bookshelf = this.app.make(`store`);
    const Reminder = bookshelf.model(`reminder`);
    const name = this.request.body.data.attributes.name;
    const done = this.request.body.data.attributes.done;

    const reminder = new Reminder({ name, done });
    return reminder.save();
  }
}
```

But, right now this we have no way of knowing which list our new reminder belongs to.
So, let's look at the incoming request for storing a new reminder:

```json
{
  "data":
    {
      "type": "reminders",
      "attributes": {
        "name": "Water Plants",
        "done": "false"
      },
      "relationships": {
        "list": {
          "data": {
            "type": "lists",
            "id": "1"
          }
        }
      }
    }
}
```

Here we can see that the id for our list is buried deep within `data.relationships.list.data.id`.
So, we'll need to grab that from our request and set it on our new reminder model:

```js
data() {
  const bookshelf = this.app.make(`store`);
  const Reminder = bookshelf.model(`reminder`);
  const name = this.request.body.data.attributes.name;
  const done = this.request.body.data.attributes.done;
  const list_id = this.request.body.data.relationships.list.data.id;

  const reminder = new Reminder({ name, done, list_id });
  return reminder.save();
}
```

Now, we can create a new reminder by running the following cURL request:

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "data":
    {
      "type": "reminders",
      "attributes": {
        "name": "Water Plants",
        "done": "false"
      },
      "relationships": {
        "list": {
          "data": {
            "type": "lists",
            "id": "1"
          }
        }
      }
    }
}' "http://localhost:3000/reminders"
```

And our response:

```json
{"links":{"self":"http://localhost/reminders"},"data":{"type":"reminders","id":"1","attributes":{"name":"Water Plants","done":"false"},"links":{"self":"http://localhost/reminders/1"}}}
```

[Next - Including Related Models](./related-models.md)
