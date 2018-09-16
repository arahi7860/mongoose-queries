# DB Queries with Mongoose

## Learning Objectives

- Understand the concepts behind CRUD
- Write queries using mongoose to get and put data from mongoDB
- Understand controllers and how they help us trigger queries
- Understand seed files and seeding the database.

## Using models to perform queries (5 min / 0:10)

In the last lesson you built out your schemas and models, but you didn't get a chance to use them. So what's the point?!

Today we're going to use the heck out of some models.

But...what do we do with them? We perform queries on the database!

Alas, lets consult the documentation to see.

https://mongoosejs.com/docs/queries.html

These are all the `methods` that are attached to our model. They allow us to perform queries using the models we already built, which represent the data we've described in a schema.

Just like we created `classes` with `class methods` in our OOP lesson (remember the gladiator lab?), our models have methods on them that we can call to perform operations.

## CRUD (15 min / 0:25)

CRUD is just an acronym. It doesn't mean "junk". It stands for:

- **C**reate
- **R**ead
- **U**pdate
- **D**elete

These 4 operations are the foundation of how you will interact with your data and database.

Any read/write operations can be put into one of these 4 categories.

- **CREATE** means we create new data, for the first time. It didn't exist previously.
- **READ** means we search for data that already exists in the database.
- **UPDATE** means we search for data that already exists in the database, and make changes to it.
- **DELETE** means....well, you know what delete means.

### You do: identifying CRUD

> 5 minute exercise, 5 minute review

Let's look at the [query methods](https://mongoosejs.com/docs/queries.html) we have available and see if we can map them to each of the CRUD actions.

Pair up with a partner on the other side of the room and spend 5 minutes looking at the methods from the documentation.

Additionally, some of these methods are designed to operate on a single record, and others on multiple records. Include that in your sorting.

Here are the methods available. Copy paste this list into your favorite text editor. Next to each one, write the action that it describes and either "one" or "multiple"

```js
Model.deleteMany()
Model.deleteOne()
Model.find()
Model.findById()
Model.findByIdAndDelete()
Model.findByIdAndRemove()
Model.findByIdAndUpdate()
Model.findOne()
Model.findOneAndDelete()
Model.findOneAndRemove()
Model.findOneAndUpdate()
Model.replaceOne()
Model.updateMany()
Model.updateOne()
```

## Anatomy of a query (15 min / 0:30)

Now we'll take a look at how we actually write and use these queries in our javascript code.

Each model represents a collection of documents in our database. So we always have to start with the model we want to work on.

```js
// assume Author is imported for the rest of these
const Author = require("./models/author")

Author
```

Then we add the method we want to run

```js
Author.find()
```

Each method takes `parameters` which is what we can pass in to narrow down our search.

```js
Author.find({ firstName: "Robert" })
```

In this case, we are looking for **all** authors with a firstName of "Robert"

So, that's great. What do we do with the result of our query? How do we get the value out?

### Aside: Promises

Promises are a much bigger concept in javascript programming that we don't spend a lot of time on. But it's worth covering here in mongoose territory.

Many operations in javascript are what's called `asynchronous`. Things like database calls, AJAX requests, and file/disk operations are asychronous. This basically means that they don't execute immediately, so we need to structure our code to handle what gets returned to us when the code finishes running.

> Side side note: [You Don't know JS](https://github.com/getify/You-Dont-Know-JS/tree/master/async%20%26%20performance) is a fantastic FREE online book and has some really in-depth explanations about async things.

Anyway, one of the ways we can handle asynchronous operations is by using promises. Promises give us a nice syntax to deal with the results of a function that takes an unknown amount of time to complete.

So for example, we can't do something like this when dealing with mongoose queries:

```js
let robs = Author.find({ firstName: "Robert" })
console.log(robs) // => undefined
```

`robs` is undefined in the console.log because javascript immediately jumps to run the next line of code before our `.find()` operation is finished.

Additionally, what gets returned from the `.find()` operation is called a `promise`. It's not the value of the find operation. What gets returned is a `promise` to give you the value once it's done executing.

In order to make the promise return something, we use `.then()`

```js
Author.find({ firstName: "Robert" }).then(authorResult => {
  console.log(authorResult)
})
```

`.then()` is a higher order function, so it takes a function as an argument, and the function gets passed the results of the `.find()` operation. Therefore, we can give it whatever name we want.

One more thing. We can't deal with promises by doing this either.

```js
let result = Author.find({ firstName: "Robert" }).then()
console.log(result)
```

We'll get something like this back:

```js
Promise { <pending> }
```

Super unhelpful.

Anyway, now that we know all this, let's go back to working with our queries.

## Anatomy of a query part II (5 min / 0:35)

We can search by any property that we have already declared in our schema definition. If we look at the `User` model from our previous lesson we can see what we've got to work with.

```js
const User = new Schema({
  email: String,
  password: String,
  tweets: [
    {
      type: Schema.Types.ObjectId,
      ref: "Tweet"
    }
  ]
})
```

We'll cover how to query references like `tweets` in a bit. For now let's just use `email` and `password` as parameters.

Now that we know how what methods we have available, and what stuff we can search by, let's write some sample queries!

### You do: 2 samples

For each of these, use a promise and console log the result of the query.

Search for a user with the email address of `test@example.com`

<details>
  <summary>
  Solution
  </summary>

```js
User.findOne({ email: "test@example.com" }).then(result => {
  console.log(result)
})
```

</details>

Search for a user with the password of `password1234`

<details>
  <summary>
  Solution
  </summary>

```js
User.findOne({ password: "password1234" }).then(result => {
  console.log(result)
})
```

</details>

## You do: Live query exercises (20 min / 0:55)

> 10 min exercise, 10 min review

Clone down the exercise repository here:
https://git.generalassemb.ly/dc-wdi-node-express/mongoose-queries-exercise

**Follow the setup instructions!!!**

Work through the prompts in `index.js` and write code for each one to make the query magic happen!

## Break (10 min / 1:05)

## Anatomy of a query part III (15 min / 1:20)

Now we know how to query some basic properties, lets talk about querying relations.

Let's look at our Tweet and Comment schemas this time.

```js
const Comment = new Schema({
  content: String,
  createdAt: {
    type: Date,
    default: Date.now()
  },
  author: {
    type: Schema.Types.ObjectId,
    ref: "User"
  }
})

const Tweet = new Schema({
  content: String,
  createdAt: {
    type: Date,
    default: Date.now()
  },
  author: {
    type: Schema.Types.ObjectId,
    ref: "User"
  },
  comments: [Comment]
})
```

Both of these have a `ref` back to the User model. Which means we should be able to start with a tweet and look up the user, or start with a user and look up a tweet or comment.

A regular query will return something like this:

```js
// assuming a query of:
User.findOne({email: "elmerfudd@gmail.com"}).then(user => {
  console.log(user)
})

// we get the result:

{
  "_id" : ObjectId("5b9ae172fdf84f1823fc7412"),
  "email" : "elmerfudd@gmail.com",
  "password" : "elmerfudd",
  "tweets" : [ ObjectId("5b9ae172fdf84f1823fc7418"), ObjectId("5b9ae172fdf84f1823fc7417") ],
  "__v" : 1
}
```

Note the `tweets` array. It contains a list of IDs, but no actual content. That's fine! That's what we told mongoose to do in our schema.
Instead of storing the value inside of the User, it stores the tweet in its own collection and just inserts an ID (as a reference) so that we can look it up.

So, in order to get the actual value, we have to use a method called `.populate()`. We tell `.populate()` what model it should look up, and it pulls in the contents for us.

```js
User.findOne({email: "elmerfudd@gmail.com"}).populate('tweets').then(user => {
  console.log(user)
})

// we get the full tweet instead of it just being an ID!
{
  tweets: [
    {
      createdAt: "2018-09-13T22:15:14.503Z",
      comments: [],
      _id: "5b9ae172fdf84f1823fc7418",
      content: "Kiww da wabbit!",
      author: "5b9ae172fdf84f1823fc7412",
      __v: 0
    },
    {
      createdAt: "2018-09-13T22:15:14.503Z",
      comments: [],
      _id: "5b9ae172fdf84f1823fc7417",
      content:
        "Shh. Be vewy vewy quiet. I'm hunting wabbits! Huh-huh-huh-huh!",
      author: "5b9ae172fdf84f1823fc7412",
      __v: 0
    }
  ],
  _id: "5b9ae172fdf84f1823fc7412",
  email: "elmerfudd@gmail.com",
  password: "elmerfudd",
  __v: 1
}
```

> Note: Populate is specific to mongoose, it's a little different than how we do it in the command line. See [$lookup](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/) for that method.

Super cool right! Populate is an easy way to do what's called a "join". It grabs data from two different places, joins it together, and returns it all to us.

Time for more practice!

## You do: More query practice (20 min / 1:40)

> 10 min exercise, 10 min review

Work through the second part of the query practice in the `mongoose-queries-exercise` repo.

## Break (10 min / 1:50)

## Linking models to controllers (10 min / 2:00)

Let's make our models work for us now.

In the last lesson we set up our controllers to just render a simple page. For example, our `application.js` controller looks something like this:

```js
module.exports = {
  index: (req, res) => {
    res.render("index", { page: "homepage" })
  }
}
```

Let's start by requiring our Tweet model at the top of the file.

```js
const { Tweet } = require("../models/Tweet")
```

> Why do we have to use { Tweet } instead of just Tweet?

Now we'll use the model to get data from the database, and pass it into the render function.

```js
module.exports = {
  index: (req, res) => {
    Tweet.find({})
      .populate("author")
      .then(tweets => {
        res.render("index", { tweets })
      })
  }
}
```

> What if we have 1000 records in the database? How can we limit the number of records we retrieve at once?

> How can we change the order in which things are displayed? Like show the newest tweets first?

Verify that this works by visiting the route for the application controller. Where are the routes defined? What url do we visit to trigger this controller action?

## You do: Add models to User controller (20 min / 2:00)

> 10 min exercise, 10 min review

For each of the methods in our user controller, we want to peform some kind of action. Let's take a look at the methods we have now:

```
show
new
create
```

(We can ignore `new` for now because it'll require input from a form, which we'll cover in the views lesson)

Think about which CRUD method each one should map to. Then, (using the [mongoose docs](http://mongoosejs.com/docs/queries.html) for reference!) write your queries to perform the actions you want.

<details>
  <summary>
  Solution
  </summary>

```js
const User = require("../models/User")

module.exports = {
	show: (req, res) => {
		User.findById(req.params.id)
			.populate({
				path: "tweets",
				options: { limit: 5, sort: { createdAt: -1 } }
			})
			.then(user => {
				res.render("index", { user })
			})
	},
	new: (req, res) => {
		res.render("index")
	},
	create: (req, res) => {
		User.create({
			email: req.body.email,
			password: req.body.password
		}).then(user => {
			res.redirect(`/user/${user._id}`)
		})
	}
}

```
</details>

If you get done early, go ahead and do the same for the Tweets controller.

## We do: Add models to Tweet controller (10 min / 2:10)

Let's now do the same for our tweets.

```js
const { Tweet, Comment } = require("../models/Tweet")
const User = require("../models/User")

module.exports = {
  show: (req, res) => {
    Tweet.findOne({ _id: req.params.id })
      .populate("author")
      .exec(function(err, tweet) {
        Comment.populate(tweet.comments, { path: "author" }, function(
          err,
          comments
        ) {
          tweet.comments = comments
          res.render("index", tweet)
        })
      })
  },
  new: (req, res) => {
    User.find({}).then(users => {
      res.render("index", { users })
    })
  },
  create: (req, res) => {
    Tweet.create({
      content: req.body.tweet.content,
      author: req.body.author
    }).then(tweet => {
      User.findOne({ _id: req.body.author }).then(user => {
        user.tweets.push(tweet)
        user.save(err => {
          res.redirect(`/tweet/${tweet._id}`)
        })
      })
    })
  },
  update: (req, res) => {
    let { content, author } = req.body
    Tweet.findOne({ _id: req.params.id }).then(tweet => {
      tweet.comments.push({
        content,
        author
      })
      tweet.save(err => {
        res.redirect(`/tweet/${tweet._id}`)
      })
    })
  }
}
```

Let's verify that this stuff works!

## We Do: Seed the Database (20 min / 2:30)

We want our database to contain data when we start our application, or when we share it with someone else and have them work on it.

Instead of manually creating users and tweets by typing them into the front end, we can set up a seed file that will populate the data with some starter data.

Because we're using mongoose in our project, we can also use the models we've already created to create data for us.

### Set Up Seed File

Here's what we're gonna do:

1. Create a new `seed.js` file in `db`.
1. In `seed.js`, require the files containing our model definition and seedData:
   - Require the `User.js` file and save it to a variable called `User`
   - Require the `Tweet.js` file and save it to a variable called `Tweet`
1. Add a method that hashes our passwords for us (using a library called bcrypt)
1. Write Mongoose queries that accomplish the following...
   - Clears the database of all data, and `.then`...
   - Inserts our seed data into the database, and `.then`...
   - Calls `process.exit()` (which exits the script).

Here's the intitial setup:

```js
const User = require("../models/User")
const { Tweet } = require("../models/Tweet")

User.find({}).remove(() => {
  Tweet.find({}).remove(() => {})
})
```

Now that we've removed all the data in there, we'll add a user record.

```js
...

User.find({}).remove(() => {
  Tweet.find({}).remove(() => {
    let bugs = User.create({
      email: "bugsbunny@gmail.com",
      password: createPassword("bugsbunny")
    })
  });
});
```

Alright, we've created the user. Now we want to associate some tweets with that user. To do that, we'll use mongoose's `Promise` feature, which allows us to chain functions using `.then()`

```js
...

User.find({}).remove(() => {
  Tweet.find({}).remove(() => {
    let bugs = User.create({
      email: "bugsbunny@gmail.com",
      password: "bugsbunny"
    }).then(user => {
      // user is the user object we just made using .create

      // Promise.all will run all of the provided functions simultaneously, and return when they're finished
      Promise.all([
        Tweet.create({
          content: "eh, what's up doc?",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        }),
        Tweet.create({
          content: "That's all, folks!",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        })
      ]).then(() => {
        // our second .then ensures that this code runs after all the previous code is done
        user.save(err => console.log(err));
        // .save writes the record to the database.
      });
    });
  });
});
```

That will create us one User and two tweets. Here's the full seedfile that includes a few more.

<details>
  <summary>
  seed.js
  </summary>

```js
const User = require("../models/User");
const { Tweet } = require("../models/Tweet");

User.find({}).remove(() => {
  Tweet.find({}).remove(() => {
    let bugs = User.create({
      email: "bugsbunny@gmail.com",
      password: "bugsbunny"
    }).then(user => {
      Promise.all([
        Tweet.create({
          content: "eh, what's up doc?",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        }),
        Tweet.create({
          content: "That's all, folks!",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        })
      ]).then(() => {
        user.save(err => console.log(err));
      });
    });

    let daffy = User.create({
      email: "daffyduck@gmail.com",
      password: "daffyduck"
    }).then(user => {
      Promise.all([
        Tweet.create({
          content: "Who's this Duck Dodgers any how?",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        }),
        Tweet.create({
          content: "You're dethpicable.",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        })
      ]).then(() => {
        user.save(err => console.log(err));
      });
    });

    let elmer = User.create({
      email: "elmerfudd@gmail.com",
      password: "elmerfudd"
    }).then(user => {
      Promise.all([
        Tweet.create({
          content:
            "Shh. Be vewy vewy quiet. I'm hunting wabbits! Huh-huh-huh-huh!",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        }),

        Tweet.create({
          content: "Kiww da wabbit!",
          author: user._id
        }).then(tweet => {
          user.tweets.push(tweet);
        })
      ]).then(() => {
        user.save(err => console.log(err));
      });
    });
  });
});

```

</details>

### Running the Seed File

1. Run `node db/seed.js` in the terminal.
2. `ctrl-c` after a few seconds to exit out of the process.
3. Then run `mongo` in the terminal and do a `.find()` on your data to verify it's all there!
