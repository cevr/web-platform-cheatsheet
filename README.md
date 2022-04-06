# web-platform-cheatsheet

Use the platform - but how?

If you've ever heard the phrase "use the platform" but don't know what that means or where to start, here's some things you can copy paste.

Lots of this stuff I learned from using [Remix](https://remix.run/)

# URLSearchParams - Stringify Search params

```ts
const data = {
  profile: "me",
  name: "cristian",
};

const params = new URLSearchParams(data);

fetch(`/api/endpoint?${params.toString()}`);
```

# Download file

You may be tempted to use javascript, but there's an easier way

```html
<a href="/pdf-endpoint.pdf" download>Download pdf</a>
```

# Forms

## Get form data (using React)

Let go of control

```tsx
function FormComponent() {
  // more efficient (no more rendering on input change)
  return (
    <form
      onSubmit={(event) => {
        const formData = new FormData(event.target);
        const email = formData.get("email");
        const password = formData.get("password");
        const data = Object.fromEntries(formData); // plain object
        doStuff(data);
        event.preventDefault(); // if you're not using a framework that uses the platform
      }}
    >
      <input type="email" name="email" />
      <input type="password" name="password" />
    </form>
  );
}
```

## Buttons as values

```tsx
function FormComponent() {
  const [viewType, setViewType] = React.useState<"list" | "grid">("list");
  return (
    <div>
      <form
        onSubmit={(event) => {
          const formData = new FormData(event.target);
          const view = formData.get("view");
          setViewType(view);
        }}
      >
        <button type="submit" name="view" value="list">
          List view
        </button>
        <button type="submit" name="view" value="grid">
          Grid view
        </button>
      </form>
      <div>
        {viewType === "grid" ? <GridView /> : null}
        {viewType === "list" ? <ListView /> : null}
      </div>
    </div>
  );
}
```

Doesn't work with all browsers (only in Safari at the time of this commit), here's a little helper function that accounts for this:

```tsx
function getFormData(event: React.FormEvent<HTMLFormElement>): FormData {
  const formData = new FormData(event.target as HTMLFormElement);

  const submitter = (event?.nativeEvent as any)
    ?.submitter as HTMLInputElement | null;
  if (submitter?.name) {
    formData.set(submitter.name, submitter.value);
  }
  return formData;
}

// usage

<form
  onSubmit={(event) => {
    const formData = getFormData(event);
    const view = formData.get("view");
    console.log(view); // works!
    event.preventDefault();
  }}
>
  <button type="submit" name="view" value="list">
    List view
  </button>
  <button type="submit" name="view" value="grid">
    Grid view
  </button>
</form>;
```

## Forms in different trees (form html attribute)

Got this from [@ryanflorence](https://twitter.com/ryanflorence/status/1502355738999463945/photo/2)

```tsx
<Sidebar>
  <form id="filter-form" onSubmit={event => {
    const formData = new FormData(event.target)
    const search = formData.get('search')
    const brands = formData.getAll('brand')
    event.preventDefault()
  }}>
    <label>
      <input type="checkbox" name="brand" value="nike" />
      Nike
    </label>
    <label>
      <input type="checkbox" name="brand" value="adidas" />
      Adidas
    </label>
  </form>
</Sidebar>

//...

<ResultsTable>
  <input name="search" form="filter-form"/>
  <button type="submit" form="filter-form">search</button>
</ResultsTable>
```
