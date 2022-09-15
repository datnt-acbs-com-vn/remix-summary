# Handle api in Remix

Remix use Web Fetch API for all http handling.

```js
const res = await fetch(url);
```

That `res` is an instance of `Response`. And you can make a `response` yourself:

```js
const res = new Response(JSON.stringify({ hello: "there" }));
const json = await res.json();
console.log(json);
// { hello: "there" }
```

# LoaderFunction and useLoaderData

In Remix, each route module is responsible for getting its own data.

To load data in a Remix route module, you use a `loader`. This is simply an async function you export that returns a response, and is accessed on the component through the `useLoaderData` hook. Here's a quick example:

```js
import type { LoaderFunction } from "@remix-run/node";
import { json } from "@remix-run/node";
import { useLoaderData } from "@remix-run/react";
import type { Sandwich } from "@prisma/client";

import { db } from "~/utils/db.server";

type LoaderData = { sandwiches: Array<Sandwich> };

export const loader: LoaderFunction = async () => {
  const data: LoaderData = {
    sandwiches: await db.sandwich.findMany(),
  };
  return json(data);
};

export default function Sandwiches() {
  const data = useLoaderData<LoaderData>();
  return (
    <ul>
      {data.sandwiches.map((sandwich) => (
        <li key={sandwich.id}>{sandwich.name}</li>
      ))}
    </ul>
  );
}
```
