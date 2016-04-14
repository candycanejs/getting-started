# Working with KNEX and Bookshelf

So far we have a grasp of working with actions, request information, and returning JSON with Candycane.
Now, let's start working with our database to show persisted data.

## Understanding the KNEX Connection

By default, Candycane CLI has installed the `candycane-knex` addon and provider in our app.
This configures our application for KNEX and shares a single KNEX instance across all of our application.
This shared intance (or singleton), makes it much easier to talk to the database from any Action in our app.

To use this connection, let's create a new action `app/actions/insert-list.js`.

```js
import Action from 'candycane/dist/http/action';

export default class extends Action {
  data() {

  }
}
```

And, we will need to register this new action with our router:

```js
this.post(`/insert`, `insert-list`);
```

Now for this, we will want to take request data from our POST request and insert a new row into our `lists` table.
We'll start by getting the required information from our request object:


```js
data() {
  const title = this.request.body.title;
}
```

## Retrieving Instances from the Application Container

In Candycane, every Action has the Application instance available as `this.app`.
On this container instance, we can call `make` to find registered modules and instances.
This is useful so that addons like `candycane-knex` can share behavior across the application.

During the application boot process, the `candycane-knex` addon registers the fully configured KENX connection as `db` within the app container.
So to retrieve this connection, we can run ``this.app.make(`db`)`` within our action.
Once we have our connection, it is just a matter of running an insert using KNEX:

```js
data() {
  const title = this.request.body.title;
  const knex = this.app.make(`db`);

  return knex(`lists`).insert({title}).then((ids) => {
    return {
      message: `Create successful`,
      ids,
    };
  });
}
```

We ran a `then` function off of the query so that we could format our data a bit more before returning it to our user.
Since Candycane is promise aware, we can easily return promises from our `data` hook instead of having to manually work through callbacks and wondering when to call `res.send`.

To see this in action, let's use CURL to create a new item in our `lists` table:

```bash
curl -X POST -H "Content-Type: application/json" -d '{
  "title": "New Years Resolution"
}' "http://localhost:3000/insert"
```

This should return back the following JSON:

```json
{"message":"Create successful","ids":[5]}
```

As we insert more rows into the database, the `ids` array will show the `id` of the newly created record in the database.

[Next - Working with Bookshelf Models](./bookshelf.md)
