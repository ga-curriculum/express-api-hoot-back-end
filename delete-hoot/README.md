# ![Express API Hoot - tktk Microlesson Name](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to tktk

## Overview

In this section, we will create a new route to delete a single hoot. This route will be a DELETE request to /hoots/:hootId, returning a JSON response with the deleted hoot.

We will be following these specs when building the route:

- CRUD Action: DELETE
- Method: `DELETE`
- Path: `/hoots/:hootId`
- Response: JSON
- Success Status Code: `200` Ok
- Success Response Body: A JSON object with the deleted hoot.
- Error Status Code: `500` Internal Server Error
- Error Response Body: A JSON object with an error key and a message describing the error.

## Define the route

Our route will listen for `DELETE` requests on `'/hoots/:hootId'`:

```
DELETE /hoots/:hootId
```

Add the following to `controllers/hoots.js`:

```jsx
router.delete('/:hootId', async (req, res) => { });
```

> 🚨 A user needs to be logged in to delete a hoot, so we should define our new route inside the **Protected Routes** section of `controllers/hoots.js`.

> 💡 Remember, in `server.js`, we defined a base path of `/hoots` for our `hootsRouter`. Therefore, we will provide the `router` above with a path of `'/:hootId'`, as this will be appended to the end of what is defined in `server.js`.

## Code the controller function

Let's breakdown what we'll accomplish inside our controller function.

First, we'll retrieve the `hoot` we want to delete from the database. We'll do this using our `Hoot` model's `findById()` method.

With our retrieved `hoot`, we need check that this `user` has permission to delete the resource. We accomplish this using an `if` condition, comparing the `hoot.author` to `_id` of the user issuing the request (`req.user._id`). Remember, `hoot.author` contains the ObjectId of the `user` who created the `hoot`. If these values do not match, we respond with a `403` status.

If the `user` has permission to delete the resource, we call upon our `Hoot` model's `findByIdAndDelete()` method. 

The `findByIdAndDelete()` accepts an ObjectId (`req.params.hootId`), used to locate the `hoot` we wish to remove from the database.

Finally, we issue a JSON response with the `deletedHoot` object.

```js
// controllers/hoots.js
router.delete('/:hootId', async (req, res) => {
  try {
    const hoot = await Hoot.findById(req.params.hootId);

    if (!hoot.author.equals(req.user._id)) {
      return res.status(403).send("You're not allowed to do that!");
    };

    const deletedHoot = await Hoot.findByIdAndDelete(req.params.hootId);
    res.status(200).json(deletedHoot);
  } catch (error) {
    res.status(500).json(error);
  }
});
```

> 🤯 As an extra layer of protection, we’ll use conditional rendering in our React app to limit access to this functionality so that only the author of a hoot can view the UI element for deleting.

## Test the route in Postman

Now that we have finished the route let's test it with Postman. We'll do this by sending a `DELETE` request to `http://localhost:3000/hoots/:hootId`. 

Create a new request in Postman. Let's name this request **Delete** and set its request type to `DELETE`. To test it out, we'll need to grab a hoot `_id` again. Feel free to use the same Postman URL we used for **Show**.

Your Postman URL should look something like this.

```
http://localhost:3000/hoots/61b63d2e397b1f34f5861ebf
```

And your request in **Postman** should look something like this.

![tktk Hunter]()

After sending the request, you should see the deleted hoot object as a response.

Congratulations! We finished building out **CRUD** functionality for `hoots`.