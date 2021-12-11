# Next.js

#### What is Next.js? and why would we use it?

Next.js calls itself a **React Framework for Production** and that name makes alot of sense, but what that really means.

**"Next.js offers alot of features that make building large scale production ready, React apps easier."**

Where React is a library and focused on building user interfaces and in the end, it's all client side javascript.

Next.js is a Framework built on React, It offers alot more than building user-interfaces which we have to add our own otherwise, like routing and other third party libraries. Out of the Box!

### Key Features of Next.js

#### 1. Server Side Rendering

Next.js supports server side rendering out of the box, Server Side Rendering is all about preparing the content of the page on the server instead on the client. It automatically pre-renders the content without any extra setup from our side. By Default. Out of the Box.

#### 2. File based routing

We don't even get a routing system with react. We have to install a third party library for routing, that's not it. The route we setup in react is in our code.

Next.js gets rid of that in-code route definition, Instead with Next.js we get file based routing system.We define our pages and routes with files and folders.

#### 3. Fullstack Capabilities

We can add some code which can work with filesystem or reaches out to database. We can have some standalone database related code with Next.js

With Next.js, It's very easy to add our own Backend API to our react project using Node JS code.

***
## Creating a new Next.js app

Make sure to have Node js Installed on the system and then run the following command:

```shell
npx create-next-app
```
### Nested Routing

Routing of the app is based on files & folders in the `pages` folder.

Each file is a route, and every new folder enables us to create nested routes.

```shell
pages > news.js = /news
pages > news > index.js = /news 
pages > news > latest.js = /news/latest
```

### Dynamic Routing

Using the same pages over and over again, with different data & content served to user.

To create a dynamic page, Change the filename to some identifer between square brackets **[identifer].js** Now next.js can load this file for different values in the page `/someID`. 

**Alternatively,** You can change the folder name to be the identifier `[someId]` and inside the folder, you can have an `index` file. 

#### Extracting the value from URL

To get the user's entered value from the URL, import the `useRouter` hook from package `next/router`

```javascript
import { useRouter } from 'next/router';

function DetailPage(){
    const router = useRouter(); 
    console.log(router.query.identifer);
    // here newsID is the identifer. i.e [newsId].js 
    const someId = router.query.newsId;

    return <h1>Detail Page</h1>
}
```

Explaination:
- import `{ useRouter }` hook from `next/router`.
- call the hook in the component, it returns a router object.
- the router object has certain pieces of data and methods we can call.
- we can get the current parameter in the url by `router.query.identier` where identifer is the filename enclosed in `[filename].js`

That's how we can build dynamic pages based on some parameter in the url.

---

# Navigation

We can use Link component instead of normal anchor tags, Link component prevents the browser default of refreshing page.

```javascript
import Link from 'next/link';

function NewsPage(){
    return <ul><li>
    <Link href="/news/next12">Next JS v12</Link>
    </li></ul>;
}
```

Explaination:
- import `Link` component from `next/link` as default.
- Use the Link component instead of anchor tag. It even has a `href` prop for url.
- Link renders an anchor element in html but without re-loading the page.

### Programmatic Navigation

We can navigate the user to some place Programmatically by using `useRouter` hook.

```javascript
import { useRouter } from 'next/router';

function MeetupItem(props) {
  const router = useRouter();
  const showDetailsHandler = () => {
    router.push('/'+props.id);
  }
  return ();
}

export default MeetupItem;
```

`router.push('/some-path')` pushes a new link to navigation stack just like `Link` component.

---
# _app.js file

There exists a file named `_app.js` in `pages` folder, It's a special file.

It's a kind of root component that nextjs will render, It uses props to receive data and destructure them to pull information out of props. i.e `{ Component, pageProps }`

- Component prop is the page that is rendered, so it will be different for each page.
- pageProps are specific props that our page might be getting.

Now we know that _app.js is the actual page content for our app, and It will change from pageA to pageB.

If we have any central layout for our entire app, It's the best place to wrap the entire app with a layout component.

```javascript
import '../styles/globals.css';
import Layout from '../components/layout/Layout';

function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}

export default MyApp;
```
---
# Pre-rendering in Next.js

In a typical react app, We would use `useEffect` and manage some state using `useState` hook to fetch data from a server.

Next.js has built-in pre-rendering but this **default built-in** pre-rendering has a problem. 
Suppose, we have a pre-rendered page on a server, and a user request it, we might be missing some data here.

So whist this is theoretically good for SEO and static site, It's not good for site which frequently changes. So we need to configure some things to fine tune it.

**How Pre-rendering works**
```
Request --> /some-route --> return [prerendered-page]
```

After the user receives the pre-rendered page the page is `hydrated` as this process is called which means now react will turn this into a Single Page Applciation and take over control. 

Then, all the the normal things in react like `useEffect` would execute after the page is received on the browser and not on server.

### Next.js gives us two forms of Pre-rendering

- Static generation (SSG)
- Server-side Rendering (SSR)

These two might sound similar but they are different and runs at different points of time.

#### Static generation (SSG)

With Static Generation, Page component is pre-rendered during build time, When built for Production.

That means, After deployment that pre-rendered would not change, atleast not by default. It's recommended for static sites.

**By default** Next.js generates your site statically on build time but if you need to add fetching to your site, we can do so by exporting a special function in our page component files.

**This only works in page component files not in other normie components**

The special function is called `getStaticProps()`, It's a reserved name for Next.js pre-rendering process.
During build time, Next.js will look for this function name and execute it during the pre-rendering process.

So the `getStaticProps()` function is first executed before executing the page component.

*`getStaticProps` is allowed to be asynchronous, means it can return a promise*

```javascript
export async function getStaticProps() {}
```

Next.js will wait till the promise is resolved, and when the data is loaded, it will generate the html files including the props.

`getStaticProps` runs on the server, that means we can securely connect to a database or access to file system as well.

Because, as it is executed on build time, It never reaches the server, not specially on client side. So we can fetch data/connect to database etc.

- Once getStaticProps() has the required props, it always needs to return an object with a property `props`.
- the props property holds another object, which is you receive in the page component function in the end.
- in the props object, we can have all the data we need in our page component.

```javascript
import MeetupList from '../components/meetups/MeetupList';

import { useState, useEffect } from 'react';
const DUMMY_MEETUPS = [
  {
    id: 'm1',
    title: 'A First Meetup',
    image: 'https://source.unsplash.com/1600x900/?landscape',
    address: 'Some address Not Native',
    description: 'This is the place decided for meetup!'
  }
];

function HomePage(props) {
  // props has the data from getStaticProps
  return <MeetupList meetups={props.meetups} />;
}

export async function getStaticProps() {
  // consume an api or connect to database
  return {
    props: {
      meetups: DUMMY_MEETUPS
    }
  };
}

export default HomePage;
```

Now, bots can find the data on our site for SEO optimization.

### Re-validating static site 

There's a problem with our current approach, that is after building, the data is never changed. The data might get outdated and we have to build and re-deploy the site again to make the changes available to visitors.
Thankfully, there's another feature in Next.js SSG

The object we returned in `getStaticProps`, we can add a key named `revalidate` and set it's value to a number.

When we do it, We unlock a new feature in Next.js.  
**\- Revalidating data**

What it does, Next.js waits till the number of seconds and then re-builds the pages if requests are coming for this page after deployment.

So we don't have to re-build and re-deploy manually all the time just because some data has  changed.

```javascript
export async function getStaticProps() {
  // consume an api or connect to database
  return {
    props: {
      meetups: DUMMY_MEETUPS
    },
    // Revalidate every 10 seconds
    revalidate: 10
  };
}
```

