## Default routes in remix

Remix support built in routes using default `app` directory

```text
./
├── app
│    ├── routes
│    │    ├── index.tsx
│    │    └── about
│    │        └── index.tsx
│    ├── components
│    ├── utils
│    ├── api
│    ├── root.tsx
│    ├── entry.client.tsx
│    └── entry.server.tsx
├── public
│    ├── trueidsdk
│    ├── fonts
│    ├── favicons
│    ├── images
│    ├── robots.txt
│    └── ...
├── styles
│    ├── tailwind.css
│    ├── app.css
│    └── no-script.css
├── types
├── server
└── ...

```

## Custom routes in remix

We can config custom routes for our remix app without using default `app` directory by edit `remix.config.js`

```js
// remix.config.js

exports.routes = async (defineRoutes) => {
  // If you need to do async work, do it before calling `defineRoutes`, we use
  // the call stack of `route` inside to set nesting.

  return defineRoutes((route) => {
    // A common use for this is catchall routes.
    // - The first argument is the React Router path to match against
    // - The second is the relative filename of the route handler
    route("/some/path/*", "catchall.tsx");

    // if you want to nest routes, use the optional callback argument
    route("some/:path", "some/route/file.js", () => {
      // - path is relative to parent path
      // - filenames are still relative to the app directory
      route("relative/path", "some/other/file");
    });
  });
};
```
