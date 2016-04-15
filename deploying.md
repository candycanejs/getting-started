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
