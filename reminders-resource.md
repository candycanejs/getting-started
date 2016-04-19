# Reminders Resource

Now let's finish building our API by creating the remaining actions for our `reminders` resource.
We'll start by registering the remaining routes:

```js
this.get(`/reminders`, 'reminders/index');
this.post(`/reminders`, 'reminders/create');
this.get(`/reminders/:id`, 'reminders/show');
this.patch(`/reminders/:id`, 'reminders/update');
this.delete(`/reminders/:id`, 'reminders/destroy');
```

Then let's create our actions:

## `reminders/index`

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`reminder`)
export default class extends Action {
  data() {
    const bookshelf = this.app.make(`store`);
    const Reminder = bookshelf.model(`reminder`);

    return Reminder.fetchAll({withRelated: `list`});
  }
}
```

## `reminders/show`

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`reminder`)
export default class extends Action {
  data() {
    const id = this.request.params.id;
    const bookshelf = this.app.make(`store`);
    const Reminder = bookshelf.model(`reminder`);

    return Reminder.where({id}).fetch({withRelated: `list`});
  }

  after(reminder) {
    if (!reminder) {
      this.setStatus(404);
      this.send({
        status: 404,
        message: `Resource not found`
      });
    }
  }
}
```

## `reminders/update`

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`reminder`)
export default class extends Action {
  data() {
    const bookshelf = this.app.make(`store`);
    const Reminder = bookshelf.model(`reminder`);

    const id = this.request.params.id;

    return Reminder.where({id}).fetch({withRelated: `list`});
  }

  after(reminder) {
    const name = this.request.body.data.attributes.name;
    const done = this.request.body.data.attributes.done;
    const list_id = this.request.body.data.relationships.list.data.id;

    if (!reminder) {
      this.setStatus(404);
      return this.send({
        status: 404,
        message: `Resource not found`
      });
    }

    reminder.set({ name, done, list_id });
    return reminder.save();
  }
}
```

## `reminders/destroy`

```js
import Action from 'candycane/dist/http/action';
import map from 'candycane-jsonapi-mapper';

@map(`reminder`)
export default class extends Action {
  data() {
    const id = this.request.params.id;
    const bookshelf = this.app.make(`store`);
    const Reminder = bookshelf.model(`reminder`);

    return Reminder.where({id}).fetch().then((reminder) => {
      if (reminder) {
        return reminder.destroy();
      }
    });
  }

  after() {
    this.setStatus(204);
    this.send();
  }
}
```

This completes our full REST API for both `lists` and `reminders`!

[Next - Deploying](./deploying.md)
