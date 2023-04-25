# **React Router DOM**

## **Tutorial**

Welcome to the tutorial! We'll be building a small, but feature-rich app that lets you keep track of your contacts. We expect it to take between 30-60m if you're following along.

![alt text](https://reactrouter.com/_docs/tutorial/15.webp)

Every time you see this '👉' it means you need to do something in the app!

The rest is just there for your information and deeper understanding. Let's get to it.

<hr/>

## **Setup**

We'll be using Vite for our bundler and dev server for this tutorial. You'll need Node.js installed for the npm command line tool.

👉️ Open up your terminal and bootstrap a new React app with Vite:

```sh
npm create vite@latest my-react-app
# follow prompts
cd my-react-app
npm install react-router-dom localforage match-sorter sort-by
npm run dev
```
You should be able to visit the URL printed in the terminal:

```sh
 VITE v3.0.7  ready in 175 ms

  ➜  Local:   http://127.0.0.1:5173/
  ➜  Network: use --host to expose
```

We've got some pre-written CSS for this tutorial so we can stay focused on React Router. Feel free to judge it harshly or write your own 😅 (We did things we normally wouldn't in CSS so that the markup in this tutorial could stay as minimal as possible.)

👉 Copy/Paste the tutorial CSS [found here](https://gist.githubusercontent.com/ryanflorence/ba20d473ef59e1965543fa013ae4163f/raw/499707f25a5690d490c7b3d54c65c65eb895930c/react-router-6.4-tutorial-css.css) into `src/index.css`

This tutorial will be creating, reading, searching, updating, and deleting data. A typical web app would probably be talking to an API on your web server, but we're going to use browser storage and fake some network latency to keep this focused. None of this code is relevant to React Router, so just go ahead and copy/paste it all.

👉 Copy/Paste the tutorial data module [found here](https://gist.githubusercontent.com/ryanflorence/1e7f5d3344c0db4a8394292c157cd305/raw/f7ff21e9ae7ffd55bfaaaf320e09c6a08a8a6611/contacts.js) into `src/contacts.js`

All you need in the src folder are `contacts.js, main.jsx, App.jsx` and `index.css`. You can delete anything else (like `assets`, etc.).

👉 Delete unused files in `src/` so all you have left are these:

```sh
src
├── contacts.js
├── index.css
├── App.jsx
└── main.jsx
```
If your app is running, it might blow up momentarily, just keep going 😋. And with that, we're ready to get started!

<hr/>

## **Adding a Router**

First thing to do is create a [Browser Router](https://reactrouter.com/en/main/routers/create-browser-router) and configure our first route. This will enable client side routing for our web app.

The `App.jsx` file is the entry point. Open it up and we'll put React Router on the page.

👉 Create and render a [browser router](https://reactrouter.com/en/main/routers/create-browser-router) in `App.jsx`
> File: `App.jsx`

```sh
import React from 'react'
import "./index.css";

# importing the react-router-dom libraries
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";`

# crearting a router
const router = createBrowserRouter([
  {
    path: "/",
    element: <div>Hello world!</div>,
  },
]);

const App = () => {
  return (
    <div>
      <RouterProvider router={router} />
    </div>
  )
}

export default App;
```
This first route is what we often call the "root route" since the rest of our routes will render inside of it. It will serve as the root layout of the UI, we'll have nested layouts as we get farther along.

<hr/>

## **The Root Route**

Let's add the global layout for this app.

👉 Create `src/routes` and `src/routes/root.jsx`
```sh
mkdir src/routes
touch src/routes/root.jsx
```
(If you don't want to be a command line nerd, use your editor instead of those commands 🤓)

👉 Create the root layout component
> File: src/routes/root.js
```sh
export default function Root() {
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={true}
            />
            <div
              className="sr-only"
              aria-live="polite"
            ></div>
          </form>
          <form method="post">
            <button type="submit">New</button>
          </form>
        </div>
        <nav>
          <ul>
            <li>
              <a href={`/contacts/1`}>Your Name</a>
            </li>
            <li>
              <a href={`/contacts/2`}>Your Friend</a>
            </li>
          </ul>
        </nav>
      </div>
      <div id="detail"></div>
    </>
  );
}
```
Nothing React Router specific yet, so feel free to copy/paste all of that.

👉 Set `<Root>` as the root route's [`element`](https://reactrouter.com/en/main/route/route#element) in `App.jsx`

```sh
# /* existing imports */
import Root from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
  },
]);
```
The app should look something like this now. ![alt image](https://reactrouter.com/_docs/tutorial/01.webp)

<hr/>

## **Handling the Not Found Errors**

It's always a good idea to know how your app responds to errors early in the project because we all write far more bugs than features when building a new app! Not only will your users get a good experience when this happens, but it helps you during development as well.

We added some links to this app, let's see what happens when we click them?

👉 Click one of the sidebar names

![img](https://reactrouter.com/_docs/tutorial/02.webp)

Gross! This is the default error screen in React Router, made worse by our flex box styles on the root element in this app 😂.

Anytime your app throws an error while rendering, loading data, or performing data mutations, React Router will catch it and render an error screen. Let's make our own error page.

👉 Create an error page component
```sh
touch src/error-page.jsx
```
> File : `src/error-page.jsx`
```sh
import { useRouteError } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}
```
👉 Set the `<ErrorPage>` as the [`errorElement`](https://reactrouter.com/en/main/route/error-element) on the root route

> File: `src/App.jsx`
```sh
# /* previous imports */
import ErrorPage from "./error-page";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
]);

const App = () => {
  return (
    <div>
      <RouterProvider router={router} />
    </div>
  )
}

export default App;
```

The error page should now look like this:

![img](https://reactrouter.com/_docs/tutorial/03.webp)

Note that [`useRouteError`](https://reactrouter.com/en/main/hooks/use-route-error) provides the error that was thrown. When the user navigates to routes that don't exist you'll get an [error response](https://reactrouter.com/en/main/utils/is-route-error-response) with a "Not Found" `statusText`. We'll see some other errors later in the tutorial and discuss them more.

For now, it's enough to know that pretty much all of your errors will now be handled by this page instead of infinite spinners, unresponsive pages, or blank screens 🙌

<hr/>

## **The Contact Route UI** 

Instead of a 404 "Not Found" page, we want to actually render something at the URLs we've linked to. For that, we need to make a new route.

👉 Create the contact route module
```sh
touch src/routes/contact.jsx
```

👉 Add the contact component UI
It's just a bunch of elements, feel free to copy/paste.
```sh
import { Form } from "react-router-dom";

export default function Contact() {
  const contact = {
    first: "Your",
    last: "Name",
    avatar: "https://placekitten.com/g/200/200",
    twitter: "your_handle",
    notes: "Some notes",
    favorite: true,
  };

  return (
    <div id="contact">
      <div>
        <img
          key={contact.avatar}
          src={contact.avatar || null}
        />
      </div>

      <div>
        <h1>
          {contact.first || contact.last ? (
            <>
              {contact.first} {contact.last}
            </>
          ) : (
            <i>No Name</i>
          )}{" "}
          <Favorite contact={contact} />
        </h1>

        {contact.twitter && (
          <p>
            <a
              target="_blank"
              href={`https://twitter.com/${contact.twitter}`}
            >
              {contact.twitter}
            </a>
          </p>
        )}

        {contact.notes && <p>{contact.notes}</p>}

        <div>
          <Form action="edit">
            <button type="submit">Edit</button>
          </Form>
          <Form
            method="post"
            action="destroy"
            onSubmit={(event) => {
              if (
                !confirm(
                  "Please confirm you want to delete this record."
                )
              ) {
                event.preventDefault();
              }
            }}
          >
            <button type="submit">Delete</button>
          </Form>
        </div>
      </div>
    </div>
  );
}

function Favorite({ contact }) {
  // yes, this is a `let` for later
  let favorite = contact.favorite;
  return (
    <Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </Form>
  );
}
```

Now that we've got a component, let's hook it up to a new route.

👉 Import the `contact` component and create a new route in `App.jsx`
```sh
# /* existing imports */
import Contact from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },

	# new route added
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
]);

# /* existing code */
```
Now if we click one of the links or visit `/contacts/1` we get our new component!

![img](https://reactrouter.com/_docs/tutorial/04.webp)

However, it's not inside of our root layout 😠

<hr/>

## **Nested Routes**

We want the contact component to render inside of the `<Root>` layout like this.

![img](https://reactrouter.com/_docs/tutorial/05.webp)

We do it by making the contact route a _child_ of the root route.

👉 Move the contacts route to be a _child_ of the root route

> File: `App.jsx`

```sh
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

You'll now see the root layout again but a blank page on the right. We need to tell the root route where we want it to render its child routes. We do that with `<Outlet>`.

Find the `<div id="detail">` and put an outlet inside

👉 Render an `<Outlet>`

> Add following code in the File: `src/routes/root.jsx`
> 
```sh
import { Outlet } from "react-router-dom";

export default function Root() {
  return (
    <>
      {/* all the other elements */}
      <div id="detail">
        <Outlet />
      </div>
    </>
  );
}
```

## **Client Side Routing**

You may or may not have noticed, but when we click the links in the sidebar, the browser is doing a full document request for the next URL instead of using React Router.

To verify this, you can open the network tab in the browser devtools and select the to see that it's requesting documents.

![img](network%20tab%20img%201.png)

After clikcing on the "Your Name" button or going on "`contacts/1`" link, we get this in our network tab : 

![img](network%20tab%20img%202.png)

Client side routing allows our app to update the URL without requesting another document from the server. Instead, the app can immediately render new UI. Let's make it happen with [`<Link>`](https://reactrouter.com/en/main/components/link).

👉 Change the sidebar `<a href>` to `<Link to>`

> Add the code in the file : `src/routes/root.jsx`
```sh
import { Outlet, Link } from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other elements */}

        <nav>
          <ul>
            <li>
              {/* this line is changed */}
              <Link to={`contacts/1`}>Your Name</Link>
            </li>
            <li>
              {/* this line is changed */}
              <Link to={`contacts/2`}>Your Friend</Link>
            </li>
          </ul>
        </nav>

        {/* other elements */}
      </div>
    </>
  );
}
```

You can open the network tab in the browser devtools to see that it's not requesting documents anymore.

![img](network%20tab%20img%201.png)

After clikcing on the "Your Name" button or going on "`contacts/1`" link, we get this in our network tab : 

![img](network%20tab%20img%203.png)

<hr/>