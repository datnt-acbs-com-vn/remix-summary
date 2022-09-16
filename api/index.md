# Handle api in Remix

Remix polyfills the fetch API on your server so it's very easy to fetch data from existing JSON APIs.

Instead of managing state, errors, race conditions, and more yourself, you can do the fetch from your loader (on the server) and let Remix handle the rest.

```js
import { json } from "@remix-run/node"; // or cloudflare/deno
import { useLoaderData } from "@remix-run/react";

export async function loader() {
  const res = await fetch("https://api.github.com/gists");
  return json(await res.json());
}

export default function GistsRoute() {
  const gists = useLoaderData();
  return (
    <ul>
      {gists.map((gist) => (
        <li key={gist.id}>
          <a href={gist.html_url}>{gist.id}</a>
        </li>
      ))}
    </ul>
  );
}
```

# LoaderFunction and useLoaderData

In Remix, each route module is responsible for getting its own data.

Each route can define a `loader` function that will be called on the server before rendering to provide data to the route. You may think of this as a "GET" request handler in that you should not be reading the body of the request; that is the job of an action.

This is simply an async function you export that returns a response, and is accessed on the component through the `useLoaderData` hook. Here's a quick example:

```js
import { json } from "@remix-run/node"; // or cloudflare/deno
import type { LoaderFunction } from "@remix-run/node"; // or cloudflare/deno

export const loader: LoaderFunction = async () => {
  return json({ ok: true });
};
```

This function is only ever run on the server.

On the initial server render it will provide data to the HTML document.

On navigations in the browser, Remix will call the function via fetch. This means you can talk directly to your database, use server only API secrets, etc. Any code that isn't used to render the UI will be removed from the browser bundle.

Using the database ORM Prisma as an example:

```js
import { json } from "@remix-run/node"; // or cloudflare/deno
import { useLoaderData } from "@remix-run/react";

import { prisma } from "../db";

export const loader = async () => {
  return json(await prisma.user.findMany());
};

export default function Users() {
  const data = useLoaderData();
  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

# loader params

Route params are passed to your loader. If you have a loader at data/invoices/$invoiceId.tsx then Remix will parse out the invoiceId and pass it to your loader. This is useful for fetching data from an API or database.

```js
export const loader: LoaderFunction = async ({ params }) => {
  params.invoiceId; // "123"
};
```

# loader request

This is a Fetch Request instance with information about the request. You can read the MDN docs to see all of its properties.
Most common cases are reading headers or the URL. You can also use this to read URL `URLSearchParams` from the request like so:

```js
export const loader: LoaderFunction = async ({ request }) => {
  // read a cookie
  const cookie = request.headers.get("Cookie");

  // parse the search params
  const url = new URL(request.url);
  const search = url.searchParams.get("search");
};
```
