# Using Tailwindcss

It is recommended using tailwind for remix app.

First install a couple dev dependencies:

```bash
npm install -D npm-run-all tailwindcss
```

Secondly, initialize a Tailwind config file:

```bash
npx tailwindcss init
```

Now we can tell it which files to generate classes from:

```js
<!-- tailwind.config.js -->

  /** @type {import('tailwindcss').Config} \*/
  module.exports = {
    content: ["./app/**/*.{ts,tsx,jsx,js}"],
    theme: {
      extend: {},
    },
    plugins: [],
  };

```

Update the package scripts to generate the Tailwind file during dev and for the production build

```js
<!-- package.json -->

{
  // ...
  "scripts": {
    "build": "run-s \"build:_\"",
    "build:css": "npm run generate:css -- --minify",
    "build:remix": "remix build",
    "dev": "run-p \"dev:_\"",
    "dev:css": "npm run generate:css -- --watch",
    "dev:remix": "remix dev",
    "generate:css": "npx tailwindcss -o ./app/tailwind.css",
    "start": "remix-serve build"
  }
  // ...
}
```

Finally, import the generated CSS file into your app:

```js
<!-- app/root.tsx -->

import type { LinksFunction } from "@remix-run/node"; // or cloudflare/deno

// ...

import styles from "./tailwind.css";

export const links: LinksFunction = () => [{ rel: "stylesheet", href: styles }];
```

If you want to use `Tailwind's @apply` method to extract custom classes, create a css file in the root directory, eg ./styles/tailwind.css:

```css
<!-- styles/tailwind.css -->

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .custom-class {
    @apply ...;
  }
}
```

Then alter how Tailwind is generating your css:

```js
<!-- package.json -->

{
// ...
"scripts": {
"build": "run-s \"build:_\"",
"build:css": "npm run generate:css -- --minify",
"build:remix": "remix build",
"dev": "run-p \"dev:_\"",
"dev:css": "npm run generate:css -- --watch",
"dev:remix": "remix dev",
"generate:css": "npx tailwindcss -i ./styles/tailwind.css -o ./app/tailwind.css",
"start": "remix-serve build"
}
// ...
}
```

It isn't required, but it's recommended to add the generated file to your `.gitignore` list:

```js

node_modules

/.cache
/build
/public/build
.env

/app/tailwind.css
```

# UI components

Using components from [this](https://remixblocks.com/)
