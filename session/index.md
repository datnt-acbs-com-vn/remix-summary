# Session in remix

Sessions are an important part of websites that allow the server to identify requests coming from the same person, especially when it comes to server-side form validation or when JavaScript is not on the page. Sessions are a fundamental building block of many sites that let users "log in", including social, e-commerce, business, and educational websites.

In Remix, sessions are managed on a per-route basis (rather than something like express middleware) in your loader and action methods using a "session storage" object (that implements the SessionStorage interface). Session storage understands how to parse and generate cookies, and how to store session data in a database or filesystem.

Remix comes with several pre-built session storage options for common scenarios, and one to create your own:

```js
    createCookieSessionStorage ()
    createMemorySessionStorage ()
    createFileSessionStorage (node)
    createCloudflareKVSessionStorage (cloudflare-workers)
    createArcTableSessionStorage (architect, Amazon DynamoDB)
    custom storage with createSessionStorage ()
```

Find out more about it in here [Session in Remix](https://remix.run/docs/en/v1/api/remix#sessions)
