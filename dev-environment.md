## Developing With Candycane

When working with Candycane, we will need our code to be constantly transpiled from ES2015 to ES5 so that Node can run our application.
To accomplish this, we can build our app once using the following command:

```bash
candycane build
```

However, since we will be constantly changing files, remembering to run this command each time can become a hassle.
Instead, we can run a slightly different command which will build our project and continue building as we develop:

```bash
candycane build --watch
```

This will build our project into a `dist` directory constantly as the files in `app` change.

## Running the Dev Server

Now that we have our code compiling, we need to run our server so that we can see our results.
To run our server once, we can run:

```bash
node dist/bin/www
```

But, similar to `candycane build`, this will only run once and isn't practical for development.
So, to reload the server after each build, we can use the `nodemon` command:

```bash
nodemon dist/bin/www
```

> **NOTE** For reloading to work, we must be running both the build watch command and nodemon.
> Tabbed terminals such as [iTerm 2](https://www.iterm2.com/) are helpful to accomplish this.

## Viewing our Server

To see if our server is running, we can go to `http://localhost:3000` and we should see the following JSON data:

```json
{
  "version": "1.0.0"
}
```

We could also view the results by making a CURL request from a new terminal:

```bash
curl -X GET "http://localhost:3000"
```

[Next - Basic Actions](./basic-actions.md)
