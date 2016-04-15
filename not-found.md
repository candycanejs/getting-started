# Not Found Errors

Right now if we go to `/lists/0`, our application gives us a `200` response that looks like:

```json
{"links":{"self":"http://localhost/lists"},"data":null}
```

But, this isn't right.
We should be getting a 404 error.
In our `app/actions/lists/show`, we can use the `after` hook to send a 404 if we don't have any data returned from our query:

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`list`)
export default class extends Action {
  data() {
    const id = this.request.params.id;
    const bookshelf = this.app.make(`store`);
    const List = bookshelf.model(`list`);

    return List.where({id}).fetch({withRelated: `reminders`});
  }

  after(list) {
    if (!list) {
      this.setStatus(404);
      return this.send({
        status: 404,
        message: `Resource not found`
      });
    }

    return list;
  }
}
```

Now we can also modify our `lists/update` action:

```js
data() {
  const bookshelf = this.app.make(`store`);
  const List = bookshelf.model(`list`);

  const id = this.request.params.id;

  return List.where({id}).fetch({withRelated: `reminders`});
}

after(list) {
  const title = this.request.body.data.attributes.title;

  if (!list) {
    this.setStatus(404);
    return this.send({
      status: 404,
      message: `Resource not found`
    });
  }

  list.set({ title });
  return list.save();
}
```

[Next - Reminders Resource](./reminders-resource.md)
