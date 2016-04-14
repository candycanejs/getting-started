# Creating Database Migrations

Candycane CLI comes with KNEX and Bookshelf integrated for database and ORM connections.
But, we will need to set up our database to accept the information from our API.

## Data Model

Before we can set up our database, we have to understand what data we will be storing.

Our API will have lists of reminders.
This means that our data has the following resources:

* List
  * Title
  * Multiple Reminders

* Reminder
  * Name
  * Done
  * Parent List

From this we will need our "Lists" to have a `hasMany` relationship to the "Reminder", and each "Reminder" will `belongTo` a single "List".

## Creating Our First Migration

To get started, let's work with our parent "List" resource and create our migration.
We'll use the KNEX CLI to generate our migration:

```bash
knex migrate:make create_lists_table
```

Now let's go into the new file in the `migrations` directory, it should look like this:

```js
exports.up = function(knex, Promise) {

}

exports.down = function(knex, Promise) {

}
```

The `up` function will be run when we want to apply this migration to our database, and the `down` function will be used if we want to undo these changes and go back to a previous database structure.

Now, we will need to start by using KNEX's schema builder to create a table for `lists` in our `up` function.
We will return this so that KNEX will wait for this to complete when running our migration:

```js
exports.up = function(knex, Promise) {
  return knex.schema.createTable(`lists`);
}
```

With our table created, we will need to add columns in the table to store our data.
We will create an auto-incrementing `id` column, and a string column to store our `title`:

```js
exports.up = function(knex, Promise) {
  return knex.schema.createTable(`lists`, (table) => {
    table.increments(`id`);

    table.string(`title`);
  });
}
```

To wrap up this migration, we will need to define our `down` function.
Here, we will use the KNEX schema builder's `dropTable` method to drop the `lists` table from our dtabase:

```js
exports.down = function(knex, Promise) {
  knex.dropTable(`lists`);
}
```

## Migration for Reminders

Now, we will follow a similar process to create a migration for our `reminders`:

```bash
knex migrate:make create_reminders_table
```

Since we need to have our reminders `belongTo` a single "List" object, we will need to setup a `list_id` column in our `reminders` table.
We will also need a boolean column for `done` and a string for `name`.

```js
exports.up = function(knex, Promise) {
  return knex.schema.createTable(`reminders`, (table) => {
    table.increments(`id`);

    table.integer(`list_id`);
    table.boolean(`done`);
    table.string(`name`);
  })
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTable(`reminders`);
};
```

## Running Migrations

Now that we have our migrations defined, we can run them from our command line:

```bash
knex migrate:latest
```

[Next - Developing With Candycane](./dev-environment.md)
