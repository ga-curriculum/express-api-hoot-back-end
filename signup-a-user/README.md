# ![Express API - Hoot Back-End - Signup a User](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to sign up a new user using Postman.

## Overview

To get started, we'll use Postman and the existing Sign Up functionality provided by the auth template to add a new user to our database. 

Before diving into Postman, let's take a moment to review a few relevant pieces of code for signing up a user.

In `server.js`, you'll notice that the `usersRouter` is mounted with a base path of `/users`. This means all routes within `usersRouter` will begin with `/users`:

```js
// server.js

app.use('/users', usersRouter);
```

Take note of the `/signup` route defined in `controllers/users.js`:

```js
// controllers/users.js

router.post('/signup', async (req, res) => {...});
```

As a result, with Postman we'll make requests to the following:

```
POST /users/signup
```

The last detail to take note of is the response (`res`) issued by our signup controller:

```js
// controllers/users.js

res.status(201).json({ user, token });
```

Notice the route returns a `token`. We'll add this `token` to a special tab in Postman, which will allow us to included it as a bearer token on future requests. This will be important, as beyond sign up and sign in, all other features in this application will be protected, requiring authenticated requests to access them.

## Test the route in Postman

In **Postman**, create a new collection called **Hoot**:

![Collection](./assets/collection.png)

With this collection, we'll be able to group a series of Postman requests, and reuse them as necessary. This makes it easier to return to previous requests later. The other advantage relates to Authorization. As discussed earlier, we'll need to include a bearer token on all future requests for `Hoot` resources. By using a collection, all requests included in the collection will be able to share the same token.

After creating the collection, locate the **Add a request** button:

![Add request](./assets/add-request.png)

We need to make a new Postman request called **Signup**. Start by updating the fields highlighted below:

- Within the **Body** tab, select **raw**, and change the **Text** dropdown to **JSON**. 

- Change the request type to a **POST** request, and provide the URL that matches the signup route:

    ```
    http://localhost:3000/users/signup
    ```

After doing this, your Postman interface should look like the following:

![Signup](./assets/signup.png)

Finally, be sure to click the **Save** button:

![Save](./assets/save.png)

Add some test account information to the **Body** in **Postman**, as shown below:

```json
{
    "username": "test",
    "password": "test"
}
```

If the request was successful, you should see something like the following response:

![Response](./assets/response.png)

We'll be using the `token` issued here in the next few steps, so be sure to *save it somewhere easily accessible*. When you copy the value of the token, **do not include the quotes**.

![Token](./assets/token.png)

Select your Hoot collection in **Postman**. Click on the **Authorization** tab. 

![Auth tab](./assets/auth-tab.png)

Set the **Type** to **Bearer Token** and add your `token` to the **Token** input field. 

![Bearer token](./assets/bearer-token.png)

Afterwards, don't forget to click the **Save button**. 

> 💡 Depending on the width of your **Postman** window, you might need to click the dropdown menu in the screenshot below to access the **Save** option. Alternatively, the **Save** button will be represented by a floppy disk icon.

Congratulations - Now that we have a `user`, we can start to building out CRUD on our `hoots`.