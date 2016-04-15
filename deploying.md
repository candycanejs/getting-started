# Deploying To Heroku

Candycane projects come with Heroku support out of the box.

To start, make sure you have an account with Heroku (you can [signup for free](https://signup.heroku.com/)).

Next, we'll need to install the [Heroku Toolbelt](https://toolbelt.heroku.com/)

Once we have installed the Toolbelt, we can create a new application on heroku:

```bash
heroku create --addons heroku-postgresql
```

This will create a new app and install the `heroku-postgresql` addon to act as our database.

Then we'll need to make sure that our code is committed:

```bash
git add .
git commit -m "Finished tutorial"
```

Now we can deploy our app to heroku by running:

```bash
git push heroku master
```

Finally, to make sure our database is ready and good to we'll need to run our migrations:

```bash
heroku run npm run migrate
```

To see where your app is located, run:

```bash
heroku apps:info
```

This will return:

```bash
Dynos:         web: 1
Git URL:       https://git.heroku.com/reminders-api.git
Owner:         ryan.tablada@gmail.com
Region:        us
Repo Size:     14 KB
Slug Size:     40 MB
Stack:         cedar-14
Web URL:       https://reminders-api.herokuapp.com/
```

The `Web URL` will be the host and root of your application.
So if we visit this url, we should see the JSON result:

```json
{
  "version": "1.0.0"
}
```

## FIN

You have now successfully built and deployed a full API using Candycane.js!
If you find any errors with these guides leave a note in the [issues](https://github.com/candycanejs/getting-started/issues/new).

If you have feature requests or questions about Candycane or Candycane CLI, open an issue on the [Candycane CLI Issues Page](https://github.com/candycanejs/candycane-cli/issues/new)
