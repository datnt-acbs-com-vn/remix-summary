# Handle unexpected errors in Remix

Remix support `Error Boundary feature` of React (500-level errors)

Error boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed. Error boundaries catch errors during rendering, in lifecycle methods, and in constructors of the whole tree below them.

With Remix, your route modules can export an `ErrorBoundary` component and it will be used. But it's even cooler because it works on the server too! Not only that, but it'll handle errors in `loaders` and `actions` too!

You can design and write your own error format announce as you like.

```js
export function ErrorBoundary() {
  return (
    <div className="error-container">
      Something unexpected went wrong. Sorry about that.
    </div>
  );`
}
```

For more example, see [this article](https://remix.run/docs/en/v1/tutorials/jokes#unexpected-errors)

# Handle expected errors in Remix

For 400-level errors, Remix offers something similar to `Error Boundaries`. It's called `Catch Boundaries` and it works almost exactly the same.

In this case, when your server code detects a problem, it'll throw a Response object. Remix then catches that thrown response and renders your `CatchBoundary`. Just like the `useLoaderData` hook to get data from the `loader` and the `useActionData` hook to get data from the `action`, the `CatchBoundary` gets its data from the `useCatch` hook. This will return the Response that was thrown.

One last thing, this isn't for form validations and stuff. We already discussed that earlier with `useActionData`. This is just for situations where the user did something that means we can't reasonably render our default component so we want to render something else instead.

```js
import { useCatch } from "@remix-run/react";

export function CatchBoundary() {
  const caught = useCatch();

  return (
    <Document title={`${caught.status} ${caught.statusText}`}>
      <div className="error-container">
        <h1>
          {caught.status} {caught.statusText}
        </h1>
      </div>
    </Document>
  );
}
```

```js
export function CatchBoundary() {
  const caught = useCatch();
  const params = useParams();
  if (caught.status === 404) {
    return (
      <div className="error-container">
        Huh? What the heck is "{params.jokeId}"?
      </div>
    );
  }
  throw new Error(`Unhandled error: ${caught.status}`);
}
```

For more example, visit the doc [here](https://remix.run/docs/en/v1/tutorials/jokes#expected-errors)
