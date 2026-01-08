# Introduction to Next.js

With a solid foundation in TypeScript now established, let's build an application in Next.js!  Next.js is a framework that's a superset of the React framework, except that it provides more support for building full-stack web applications.  Next.js allows you to create dedicated server and client components to handle front-end and back-end logic, and there a ton of built-in optimizations that allow the app to scale and run quickly and smoothly.

## Starting a new Next.js project
There are a couple of ways to create a new Next.js project.  In order to get started, you must have Node.js installed on your machine.

If you use the npm package manager, run the following command:
```
npx create-next-app@latest your-app-name-here
```
This will install the `create-next-app` package.  Go ahead and type `Y` to proceed.  From there you will be prompted if you want to use the recommended defaults for Next.js.  By default, you will have TypeScript, Tailwind and other packages and tools like ESLint, App Router, and Turbopack.  Otherwise you can configure the set-up yourself.  To bypass the prompts, do the following and use the built-in defaults or the defaults you set up yourself:
```
npx create-next-app@latest your-app-name-here --yes
```

Alternately you can install a separate package manager called `pnpm`:
```
npm install -g pnpm
```
The `pnpm` package manager is faster than npm as it uses caching and symbolic links to speed installations along.  

Once you install pnpm, you can create a Next.js app like so:
```
pnpm create next-app@latest your-app-name-here --yes
```
The defaults are the same regardless of which package manager you use.

## Starting and running the app
Make sure once you create the project that you enter the folder - which will have the same name as your project - by running `cd your-project-name-here`, changing the name appropriately.  To run your project, type `npm run dev` or `pnpm dev`.

To see the project in action, go to `localhost:3000`.  (You can configure the port number yourself to a different one.)

To stop the project, run `Ctrl`+`C` or `Cmd`+`C`.

## Folder structure
Inside your project, you will have files and folders that look like this:

![Starter folder structure for a Next.js project](./starter-folder-structure.png)

Like in a React project, you will have a `package.json` file that contains the scripts and packages for your project.  You will also recognize the `tsconfig.json` file for TypeScript projects.

The `app` folder is a starter folder that contains your CSS and tsx files.  The important file we'll start with is the `page.tsx` file, which is the home page for your project.  We'll learn how to organize our CSS files and routes very soon!

The `public` folder houses all your static content, so images, videos, etc.

You can find out more about how Next.js projects are organized, along with folder and file conventions, at https://nextjs.org/docs/app/getting-started/project-structure.

## Routing and rendering pages
Inside your app folder will be additional folders.  These folders will serve as your route names.  Here's an illustration, where each folder starts with a "/":
```
/app
    page.tsx
    /dashboard
        page.tsx
    /blog
        page.tsx
    /about
        page.tsx
        /legal
            page.tsx
/public
```
In the example above, you will have routes called `/` (for the app), `/dashboard`, `/blog`, `/about`, and `/about/legal`.  Notice how you can nest routes by adding subfolders, as demonstrated with the "legal" folder inside the "about" folder.

Inside each folder is a file called `page.tsx`, which is what is rendered when visiting the route that the file is located in.  It must be named `page`, and you can use `.tsx`, `.js`, and `.jsx` file extensions.

The `page` file will contain the component that you will export and render.  It must be a default export.  For example, you might have a component that looks like this:
```tsx
const Dashboard = () => {
    return (
        <>
            <p>Here is a component!</p>
        </>
    )
}
export default Dashboard;
```
So if you have the `page.tsx` file in a route called `/home`, you will see that page rendered!

### Dynamic routing
When you look at a particular product from a shopping site, a specific blog post, or anything with custom information, you'll want routes that show specialized information.  But it would be unwieldy to make a million pages and folders for a million products.  So dynamic routing comes to the rescue!  The way it works is that you can define slugs in your folder names.  For example, if you want to have routes like `/blog/1`, `/blog/2`, etc., you can up your project like this:

```
/app
    page.tsx
    /blog
        page.tsx
        /[blogNumber]
            page.tsx
```
Notice how the folder name `blogNumber` is wrapped inside the square brackets `[]`.  This turns the folder name into a dynamic route segment.  You can think of it as a path variable.

In the `page.tsx` file inside the `[blogNumber]` folder, you can access the value of the dynamic route segment like so:
```tsx
export default async function BlogPost(props: {
    params: Promise<{blogNumber: string}>
}) {
    const { blogNumber } = await props.params; // Notice how we're destructuring
    return (
        <p>This is blog number { blogNumber }!</p>
    );
}
```
You **must** make your component asynchronous whenever you extract a dynamic route segment.  You can have multiple variables; they would all be found inside the `Promise` and can be destructured accordingly.  If you opt to utilize a `use` function from React, then you don't need to explicitly make the component async, as the `use` function is used to grab values from Promises.

You can read up more on the `page` file at https://nextjs.org/docs/app/api-reference/file-conventions/page and learn more about dynamic routing at https://nextjs.org/docs/app/getting-started/project-structure#dynamic-routes.

### Linking to other pages
When rendering components on the client side, minimizing load times is crucial, especially if data must be loaded from an external site or a database.  Next.js accomplishes a lot of this by prefetching routes to minimize delays.  The `Link` component allows Next.js to pre-load the routes linked in them in the background, so that when a user clicks, that route is rendered effectively immediately.


We don't cover other important concepts like streaming, which is when a server sends part of a route when it's ready instead of waiting for it to fully render, and client-side transitions so that a page won't reload.  These ideas makes the `Link` component very powerful.

You can read up more on linking and navigating at https://nextjs.org/docs/app/getting-started/linking-and-navigating and the `Link` component at https://nextjs.org/docs/app/api-reference/components/link.

## Layouts and CSS
A layout is a UI that shares state and does not re-render.  Layouts are useful if you have several routes that share state and styling.

Layouts should be defined at the level where you will share content accordingly.  By default, you will have a `layout.tsx` file at the topmost level of the app. 

You are allowed to nest layouts.  A layout file must have children included so that the components that use it can render there accordingly.

```tsx
export default function MyLayout({
    children,
}: {
    children: React.ReactNode
}) {
  return (
    <>
        {children}
    </>
  )
}
```
Notice the `children` property, which is required so that content can be displayed there.

Layouts are useful for components like headers and footers.

You can read up more on layouts [here](https://nextjs.org/docs/app/api-reference/file-conventions/layout).

If you wish to apply styling that will be shared by all your components and pages in your application, you'll need a CSS file at the topmost level of your app and then import it in a layout file.  By default, you will have a `globals.css` file along with a `layout` file.

For example, you might choose to include the following CSS in your `globals.css` file inside your `app` folder:
```css
* {
    margin: 0;
    padding: 0;
}

.my-class {
    color: lightblue;
}
```

Now you can import your file in your layout file in the same folder as the CSS file above:
```tsx
import './myStyles.css`;
```

Now you can apply styles like in React by using `className` inside your components, like so:
```tsx
<p className="my-class">I love code!</p>
```

You can read up more on CSS and using Tailwind CSS [here](https://nextjs.org/docs/app/getting-started/css).  You can also check out CSS modules, which are useful for preventing class name collisions if you have the same class name used in different components with different styling.