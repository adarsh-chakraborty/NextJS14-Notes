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

## Static generation (SSG)

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
---
## Server Side Rendering (SSR)

So get `getStaticProps()` is a very important function in Next.js to ensure we feed some data to the pre-rendered pages, Now with revalidate, we can ensure that the pages are updated periodically  after depolyment.

But, sometimes even regular updates are not enough, Sometimes we really need to pre-generate the page on every request on the fly, after depolyment on the server.

Not periodically, not during the build process, If that is your goal then there's a better alternative we have.


There's another function we get export, which can again be asynchronous, that is `getServerSideProps()`.
```javascript
export async function getServerSideProps(context) {
  const req = context.req;
  const res = context.res;

  // fetch data
  return {
    props: {
      meetups: DUMMY_MEETUPS
    }
  }
}
```

This function will not run during build process but instead always on the server, on every request, after depolyment.

As `getServerSideProps()` is executed on every request, we actually have access to the request and response through props. More on it later...

---

### Static Site Generation vs Server Side Rendering  

Okay so, now we know about SSG and SSR, so which one is better? and which one should we use? and when?

Can we use both at the same time? Nope

Server side Rendering might sound better but actually it has an disadvantage, We're waiting for the page to generate at every request which can actually slow down your overall response time.

So we should only use SSR when there are multiple changes occuring at every second and even revalidate cannot help us or we need to access request object on every request for some reason *e.g. authentication*.

Conclusion: To take advantage of Caching having pre-rendered pages is alot better because rendering them on every request can slow down response time.

---

### Working with Params in SSG and SSR 

To fetch data, It might need some identifier, specially for dynamic routes, We canot use react hooks like `useRouter` in getProps functions.

Instead, we can take help of the context object which is received in these function, there we can access the params property.

```javascript
export function getStaticProps(context){
  const meetupId = context.params.meetupId;
  // Where meetupId is the Identifer. [meetupId].js
  use meetupId 
}
```

With only that, we would end up with SSG error, that `getStaticPaths` is required.

`getStaticPaths` is a function we need to export in a Page component file, if it's a dynamic page and we are using `getStaticProps`.

In that case, we need to export another function here, in the page component file. It can also be asynchronous.

```javascript
export async function getStaticPaths() {}
```
**Why we need this?**

Because, with Dynamic routes, we need to pre-generate pages for all versions of supported Ids, If we don't pre-generate the page for them, they will see a 404 page error.

So we need to have an function like getStaticPaths(), which has the job of returning an object where we describe dynamic segment values.

```javascript
export async function getStaticPaths() {
  return {
    fallback: false,
    paths: [
      {
        params: {
          // all key value pairs which leads to ur dynamic page
          meetupId: 'm1',
        }
      },
      {
        params: {
          meetupId: 'm2',
        }
      },
    ]
  }
}
```

The object must hold a property named `paths` and the value of it should be an Array. An Array of Objects to be specific, 1 Object per version of this dynamic page.

Each object inside the array must hold a params key, and the value of the param key must be an object. It will contain all the key-value pairs which leads to your dynamic page. (Suppose you have nested dynamic routing)

**fallback:** The fallback key tells if we have all the supported values in the array or just some of them.

If set to `false`, It will throw a 404 page.
If set to `true`, NextJS will try to generate a page for this meetupId dynamically on the server on the incoming request.

It's a nice feature to pre-generate some of the pages, for example, frequently visited pages and will pre-generate the missing ones dynamically.

---

# Introducing API Routes

Next.js makes it easy for us to build an API together with our frontend react app in the same project.

For this, We can use another key Next.js feature called API Routes.

#### API Routes 

API Routes are special routes, special pages which don't return html code but Instead, It's about accepting Http requests.

So basically, API routes allows us to build API Endpoints as part of this Next.js project and they will be served by the same server as Next.js app.

### Getting started with API Routes

First we need to created a folder named `api`, inside the pages folder. Just like `pages` folder, It's a reserved name to make api end points recognized by next js.

Next.js picks up any javascript files in the `api` folder and turn those files into API Endpoints which can be targetted by requests which can receive and send JSON data.

- Create a javascript file in the `api` folder which lies inside `pages` folder.
- In the javascript file, we don't work with react components, It's not about rendering or defining components.
- Instead in the file, we define functions that contains server side code, as it gets executed on the server.
- This code never gets exposed to the client.
- The function name can be anything but the important thing is to export it.
- Req and Res object will be passed to the function on every request.
- We can check the method, body or anything with the request object.

```javascript
// pages > api > new-meetup.js
function handler(req,res) {
    if (req.method === 'POST') {
        const data = req.body;
        const { title, image, address, description } = data;
    }
}
export default handler;
```

Now we can connect to a database here, Let's connect to a mongodb server.

```javascript
import {MongoClient} from 'mongodb';

async function handler(req,res){

    const client = await MongoClient.connect(process.env.MONGODB_URI);
    const db = client.db();

    const collectionName = db.collection('collectionName');

    const result = await collectionName.insertOne({req.data});

    client.close();
    res.status(201).json({message: 'Meetup Inserted'});
}
```

## Sending http requests to our API Routes

It's same as sending http request to any api route for example built-in fetch function or axios.

```javascript
import { useRouter } from 'next/router';

function NewMeetupPage(){
    const router = useRouter();
    const meetupHandler = async (enteredMeetupData) => {
    const response = await fetch('/api/new-meetup', {
      method: 'POST',
      body: JSON.stringify(enteredMeetupData),
      headers: {
        'Content-Type': 'application/json'
      }
    });

    const data = await response.json();
    console.log(data);
    // router.replace('/'); // Disables back button.
    router.push('/');
  };
}
```

## Getting data from Database

Connecting to mongodb and get all the meetups

```javascript
import { MongoClient } from 'mongodb';

export async function getStaticProps() {
  // consume an api or connect to database
  const client = await MongoClient.connect(process.env.MONGODB_URI);
  const db = client.db();

  const meetupsCollection = db.collection('meetups');

  const meetups = await meetupsCollection.find().toArray();

  client.close();
  return {
    props: {
      meetups: meetups.map((meetup) => ({
        title: meetup.title,
        address: meetup.address,
        image: meetup.image,
        id: meetup._id.toString()
      }))
    },
    revalidate: 10
  };
}
```

### Some notes: 

- If we import something that is only used in `getServerSideProps` or `getStaticProps`,It will not be a part of client side bundle.
- So depending on where you use it, Next.js will detect it automatically and exclude it from client bundle.
- We can use fetch() in Next.js server side code, Next.js supports it. Normally it's only avaiable on browsers.
- But sending fetch() requests to our own API is a bit cumbersome, we can directly write the code to connect to database and fetch required data.
- Why? Sending http request to our own API will end up connecting to database in the end and we have to wait for response. Which is redundant.

---

## Getting single item from database and passing props

Directly connecting to db to pre-render the pages.

Changed fallback to `blocking` so if a page is not available, it will try to generate it on the fly.

**Possible fallback values:**
- `false` It will directly send a 404 page.
- `true` sends a black page, and try to generate the page. We have to handle the case. 
- `blocking` Request is awaited till it tries to pre-render and serves the generated page. (No extra work required!)

```javascript
import MeetupDetails from '../../components/meetups/MeetupDetail';
import { MongoClient, ObjectId } from 'mongodb';

function MeetupDetailsPage(props) {
  const meetup = props.meetupData;
  console.log(props);
  return (
    <MeetupDetails
      src={meetup.image}
      address={meetup.address}
      title={meetup.title}
      details={meetup.details}
    />
  );
}

export async function getStaticProps(context) {
  // fetch data for single meetup
  const meetupId = context.params.meetupId;

  const client = await MongoClient.connect(process.env.MONGODB_URI);
  const db = client.db();

  const meetupsCollection = db.collection('meetups');
  console.log('trying to find with meetupId: ', meetupId);
  const meetupData = await meetupsCollection.findOne({
    _id: ObjectId(meetupId)
  });
  console.log(meetupData);
  client.close();

  return {
    props: {
      meetupData: {
        _id: meetupData._id.toString(),
        title: meetupData.title,
        address: meetupData.address,
        image: meetupData.image,
        description: meetupData.description
      }
    }
  };
}

export async function getStaticPaths() {
  const client = await MongoClient.connect(process.env.MONGODB_URI);
  const db = client.db();

  const meetupsCollection = db.collection('meetups');
  const meetups = await meetupsCollection.find({}, { _id: 1 }).toArray();
  client.close();
  return {
    fallback: 'blocking',
    paths: meetups.map((meetup) => ({
      params: { meetupId: meetup._id.toString() }
    }))
  };
}
export default MeetupDetailsPage;
```

## Adding Meta data

Adding Meta data to pages is fairly simple with Next.js.

First, we need to important a component named `Head` from `next/head`. It's a special component offered by Next.js
and then inside the Head component, we can write all our meta data like title, description etc.

```javascript
import Head from 'next/head';
function HomePage(props) {
  return (
    <>
      <Head>
        <title>React Meetups</title>
      </Head>
      <MeetupList meetups={props.meetups} />
    </>
  );
}
```

## Deploying to Vercel

Deploying next.js project is simple with vercel, just connect to github, and deploy the branch.






