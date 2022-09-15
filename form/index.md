# Form in remix

We can use both normal HTML Form and remix built-in Form component. It is recommended to use Form component built-in remix.

```js
import { Form } from "@remix-run/react";
```

# ActionFunction and useActionData

We use `ActionFunction` and `useActionData` to handle with form in remix

```js
import type { ActionFunction } from "@remix-run/node";
export const action: ActionFunction = async ({ request }) => {
  const form = await request.formData();
  const name = form.get("name"); //input tag with name=“name”
  const content = form.get("content"); //text area tag with name=“content”
  // we do this type check to be extra sure and to make TypeScript happy
  // we'll explore validation next!
  if (typeof name !== "string" || typeof content !== "string") {
    throw new Error(`Form not submitted correctly.`);
  }

  const fields = { name, content };

  const joke = await db.joke.create({ data: fields });
  return redirect(`/jokes/${joke.id}`);
};
```

If there's an error, you can return object with the error message then component can get those values from `useActionData` and display to users

The `badRequest` helper function is important because it gives us typechecking that ensures our return value is of type ActionData, while still returning the accurate HTTP status, 400 Bad Request, to the client.

```js
import { useActionData } from "@remix-run/react";

type ActionData = {
  formError?: string,
  fieldErrors?: {
    name: string | undefined,
    content: string | undefined,
  },
  fields?: {
    name: string,
    content: string,
  },
};

function validateJokeContent(content: string) {
  if (content.length < 10) {
    return `That joke is too short`;
  }
}

function validateJokeName(name: string) {
  if (name.length < 3) {
    return `That joke's name is too short`;
  }
}

const badRequest = (data: ActionData) => json(data, { status: 400 });
export const action: ActionFunction = async ({
  request,
}) => {
..........
    return badRequest({
        formError: `Form not submitted correctly.`,
    });
  }

  const fieldErrors = {
    name: validateJokeName(name),
    content: validateJokeContent(content),
  };
  const fields = { name, content };
  if (Object.values(fieldErrors).some(Boolean)) {
    return badRequest({ fieldErrors, fields });
  }
............

export default function NewJokeRoute() {
  const actionData = useActionData<ActionData>();
  return (
    <div>
      <p>Add your own hilarious joke</p>
      <form method="post">
        <div>
          <label>
            Name:{" "}
            <input
              type="text"
              defaultValue={actionData?.fields?.name}
              name="name"
              aria-invalid={
                Boolean(actionData?.fieldErrors?.name) ||
                undefined
              }
              aria-errormessage={
                actionData?.fieldErrors?.name
                  ? "name-error"
                  : undefined
              }
            />
          </label>
          {actionData?.fieldErrors?.name ? (
            <p
              className="form-validation-error"
              role="alert"
              id="name-error"
            >
              {actionData.fieldErrors.name}
            </p>
          ) : null}
        </div>
..........

```

# Form action

If you don't use this prop, form without action will use same route.

If you need to post to different route, use this prop

```js
<Form action="/projects/new" method="post" />
```

# useFormAction, useSubmit, useTransition

Please refer to document in [remix-run page](https://remix.run/docs/en/v1/api/remix#useformaction).
