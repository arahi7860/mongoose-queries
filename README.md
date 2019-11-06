[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# Mongoose Queries

In a previous lesson, you defined schemas and models, but you didn't get to use
them.

In this lesson, we'll pick up where we left off and discuss querying models from
within a Node application.

## Prerequisites

- JavaScript
- Node and NPM
- MongoDB
- Mongoose Models and Schemas

## Objectives

By the end of this, developers should be able to:

- Understand the concepts behind CRUD
- Write queries using Mongoose to get and set data in MongoDB

## Introduction

Now that we know how to define Models in Mongoose and connect them to our
database, we're ready to start using them!

But what do we use models for?

We use models for two reasons:

1. Define the structure of and relationships between of our data
1. Access our data throughout our application

The previous demonstration was on the first point; this one is on the second.

## CRUD

Everything we can do with our data can be described by CRUD. CRUD stands for:

- **C**reate
- **R**ead
- **U**pdate
- **D**elete

These 4 operations are the foundation of how you will interact with your data
and database.

Any read/write operations can be put into one of these 4 categories.

- **CREATE** means we create new data, for the first time. It didn't exist
  previously.
- **READ** means we search for data that already exists in the database.
- **UPDATE** means we search for data that already exists in the database, and
  make changes to it.
- **DELETE** means - well, you know what delete means.

## You Do: Identifying CRUD

Let's look at the [query methods](https://mongoosejs.com/docs/queries.html) we
have available and see if we can map them to each of the CRUD actions.

Divide a page of your notebook into four sections. Title each section one of the
pieces of CRUD. For each method in the list below, place it in the appropriate
section of the grid.

Here are the methods available:

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

Some of these methods are meant to operate on a single document, while others
are meant to operate on multiple documents. Looking back at your grid, add an
asterisks (`*`) to each item that only acts on a single document.

## I Do: Anatomy of a Query

Now we'll take a look at how we actually write and use these queries in our
JavaScript code. Each model represents a collection of documents in our
database. So we always have to start with the model we want to work on.

Open up [this repository](https://git.generalassemb.ly/dc-wdi-node-express/mongoose-authors) so that you can follow along.

### Create

To create a new document in our database, we'll use the `.create()` method. We
pass in to this method an object representing the document we'd like to save:

```js
Author.create({
  firstName: "F. Scott",
  lastName: "Fitzgerald",
  nationality: "United States",
  birthYear: 1896,
  books: [
    {
      title: "The Great Gatsby",
      type: "Fiction"
    }
  ]
}).then(author => {
  console.log(author);
});
```

### Read

To read documents from the database, we use `.find()` or `.findOne()`. Note that
`.find()` will always return an array of records (even if there are 0 matches)
and `.findOne()` will only ever return a single record (even if there are more
than one matches).

```js
// Using .findOne()
Author.findOne({ firstName: "Ernest" }).then(author => {
  console.log(author);
});

// Using .find()
Author.find({ nationality: "United States" }).then(authors => {
  console.log(authors);
});
```

### Update

To update a document, we can use `.findOneAndUpdate()` or
`.findByIdAndUpdate()`. If we have the ObjectID for the document, then it's
probably best to go with that option. If we don't, though, then using
`.findOneAndUpdate()` with a query works really well.

```js
Author.findOneAndUpdate(
  { firstName: "Ernest" },
  { $set: { birthYear: 1900 } },
  { new: true }
).then(authors => {
  console.log(authors);
});
```

That works well for updating a single document. What about updating every
document that matches a query?

```js
Author.updateMany(
  { nationality: "United States" },
  { birthYear: 1965 },
  { new: true }
).then(authors => {
  console.log(authors);
});
```

### Delete

```js
Author.findOneAndRemove({ lastName: "Kundera" }).then(authors => {
  console.log(authors);
});
```

## [License](LICENSE)

1. All content is licensed under a CC­BY­NC­SA 4.0 license.
1. All software code is licensed under GNU GPLv3. For commercial use or
   alternative licensing, please contact legal@ga.co.
