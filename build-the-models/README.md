# ![Express API - Hoot Back-End - Build the Models](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to define and export a mongoose model for use with an Express API.

## Overview

In this lesson, we'll define the models necessary for our application. The resources in our application will include a `User` model (included in our starter code), a `Hoot` model, and a `commentSchema`, embedded inside the `Hoot` model.

## User model

Take a moment to review the existing `userSchema`, as we'll make use of the `User` resource throughout our application. 

```js
// models/user.js

const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    username: {
        type: String,
        required: true
    },
    hashedPassword: {
        type: String,
        required: true
    },
});

userSchema.set('toJSON', {
    transform: (document, returnedObject) => {
        delete returnedObject.hashedPassword;
    }
});

module.exports = mongoose.model('User', userSchema);
```

Thanks to the `verifyToken` middleware function, all protected routes in our application will have access to an object representing the logged in `user`, through the `req` object. For our purposes, the most relevant properties on the `user` object will be its ObjectId (`_id`) and the `username`. Additionally, the `User` model will be ***referenced*** by `hoot` documents.


## Create the model file `models/hoot.js`

Next we'll add the `models/hoot.js` file.

Run the following command in your terminal:

```bash
touch models/hoot.js
```

> 💡 We use a **singular** naming convention for model files, as a single file will always export just one model.

## Create the `hootSchema`

Before we're able to define our schema and model, we must first import the `mongoose` library into `models/hoot.js`:

```js
// models/hoot.js

const mongoose = require('mongoose');
```

Next we'll define the schema, which will act as a blueprint for the shape of `hoot` data in our database.

Our `hootSchema` will consist of a `title` property, a `text` property, and a `category` property, all `required`, with a `type` of `String`. The `category` property will differ slightly from the others, here we will use `enum` to limit its allowed values to the following:

```js
['News', 'Sports', 'Games', 'Movies', 'Music', 'Television']
```

The `hootSchema` will also have an `author` property, which will act as a reference to the `User` who created the `hoot`.

Update `models/hoot.js` with the following:

```js
// models/hoot.js

const hootSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: true,
    },
    text: {
      type: String,
      required: true,
    },
    category: {
      type: String,
      required: true,
      enum: ['News', 'Sports', 'Games', 'Movies', 'Music', 'Television'],
    },
    author: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  },
  { timestamps: true }
);
```

> 💡 Notice this inclusion of `{ timestamps: true }`. This will give our `hoot` documents `createdAt` and `updatedAt` properties. We can use the `createdAt` property when we want to display the date a hoot post was made.

## Register the model

Now that we've defined our schema, we'll tell Mongoose to create a collection in MongoDB and validate that collection's data using the schema. We do this using the `mongoose.model` method:

```js
// models/hoot.js

const Hoot = mongoose.model('Hoot', hootSchema);
```

## Export the model

Finally, we'll export the `Hoot` model so that the rest of our application has access to it:

```js
// models/hoot.js

module.exports = Hoot;
```

## Create the `commentSchema`

The last step is to define a `commentSchema` in `models/hoot.js`. The `commentSchema` will be embedded inside `hootSchema`, meaning each `hoot` document will contain its own associated comments. These comment subdocuments will be accessible through a `comments` property on a `hoot`.

Much like the `hootSchema`, the `commentSchema` will store a reference to the `author` and include `createdAt` and `updatedAt` `timestamps`.

Add the following to `models/hoot.js`:

```js
// models/hoot.js

const commentSchema = new mongoose.Schema(
  {
    text: {
      type: String,
      required: true
    },
    author: { type: mongoose.Schema.Types.ObjectId, ref: 'User' }
  },
  { timestamps: true }
);
```

> 💡 We don't need to compile the `commentSchema` into a model, or export it, as it is embedded inside the parent `hootSchema`. As a result, any functionality related to the `comments` resource will need to go through the `Hoot` first.

Next we'll need to update the `hootSchema` with a `comments` property:

```js
// models/hoot.js

const hootSchema = new mongoose.Schema(
  {
    title: {
      type: String,
      required: true,
    },
    text: {
      type: String,
      required: true,
    },
    category: {
      type: String,
      required: true,
      enum: ["News", "Sports", "Games", "Movies", "Music", "Television"],
    },
    author: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
    comments: [commentSchema], // add here
  },
  { timestamps: true }
);
```