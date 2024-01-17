
# Next JS 14 Notes

Next JS uses file based routing so each folder inside pages/app directory is a route and the code goes inside *page.tsx* file.
 
#### Special Files in NEXT JS

    page.tsx 
    layout.tsx  
    template.tsx // same as layout but creates new instances
    not-found.tsx // 404 page
    loading.tsx // Error Boundary with Suspense
    error.tsx // Error Handling


---
**Routing**  
about/  ->>>> example.com/about  
[slug]/ ->>>> example.com/{slug} (Dynamic)  
(group)/ ->>> example.com/??? (Does not effect URL)  

### Layout.jsx

Next.JS creates Layout file automatically if it's not present.Layout defines the UI that is shared across multiple pages.


### Page.tsx

Exports a default React component that defines the UI for the page. (Leaf Route)

**All Components in Next14 is a server component by default.**

```javascript
async function getNotes(){
  const res = await fetch("db.url", { cache: 'no-store'}); // no caching
  const data = await res.json();
  return data.items;
}

export default async function NotesPage(){
  const notes = await getNotes();

return (<ul>notes.map(el => <li key={el}>{el}</li>)</ul>);
}
```

### Dynamic Routes
Name the folder inside []. Example [notes]/page.tsx
Because this is a dynamic route it won't automatically cache the pages so for that we need to set revalidate flag in the fetch request

#### Incremental Static Regeneration (ISR)
Regenerates pages on the server if it is older than certain number of seconds.

```javascript
async function getNotes(noteId){
  const res = await fetch(`db.url/notes/${noteId}`, { next: { revalidate: 10}}); 
  const data = await res.json();
  return data.items;
}

export default async function NotesPage({params}){
  const notes = await getNotes(params.noteId);

return (<ul>notes.map(el => <li key={el}>{el}</li>)</ul>);
}
```

If we want to pre-render these pages we can export a function called `generateStaticParams`.

### Loading.tsx 

We can add Loading.tsx file to any folder. The file should export a default function to define some kind of loading indicator/fallback UI. 

export default function Loading(){
 return (<div>Please wait.. Loading.</div>)
}

**Content is streamed to the UI.**


### error.tsx

export  default function Error({error, reset}) {
  // Retry handler is given in reset prop. Link to a button.
  // Error details is given in error prop.
 return (<div>An Error occured.</div>)
}


### Data Mutation

"use client" directive is used at the top of page to make it a client component.
Then all the react hooks can be used in that component.

Whenever the UI needs to be refreshed we can call router.refresh()

```javascript
  const router = useRouter();

  const createNotes(){
  // fetch API call
  router.refresh(); // refreshes data in the UI
}
```

# App Router


#### Error Handling in Nested Routes
Errors bubble up to the closed error boundary.
A error.tsx file will cater to errors for all it's nested  child componenets.

The placement of error.tsx file determines which parts of the applications are effected when an error occurs.
