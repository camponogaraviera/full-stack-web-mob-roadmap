<div align='center'>
  <h1>ReactJs</h1>
</div>

# Table of Contents

- About
- Creating a React-based project
- Scaffolding the project
- Short Notes
  
# About

[React.js](https://github.com/facebook/react) is a client-side JavaScript framework. For full stack, take a look at [Next.js](https://github.com/vercel/next.js) which is built on top of React to extend React's functionalities including server-side rendering by default and simplified routing (replacing React-Router).

React.js (Web) and react-native (Mobile) primarily uses JavaScript, however, it also utilizes JSX (JavaScript XML), a syntax extension for JavaScript that allows one to create declarative UI components using a syntax that closely resembles HTML or XML.

- Use React.js if you need to build large-scale web applications with client-side rendered functionality.
- Use Next.js if you need Server-Side Rendering (SSR), SEO, SEM, and automatic code splitting.

## Glossary 

JAMstack app: any app that uses JavaScript, API, and Markup.

SSR: server-side rendering means that webpages are run in the server before they are sent back to the client.

SEO: search engine optimization is a marketing strategy used to rank a webpage at the top in Google search. It is a built-in feature in Next.js.

SEM: search engine marketing uses paid platforms to scale marketing visibility.

# Creating a React-based project

## Using [Next.js](https://react.dev/learn/start-a-new-react-project#nextjs-pages-router):

```bash
npx create-next-app@latest
```

## Using [Create React App](https://create-react-app.dev/):

- Yarn:
```bash
yarn create react-app my-app
```
- NPM:
```bash
npx create-react-app app-name
```

- Start the development server:
  
```bash
cd app-name && yarn start
```

- Create a production build for optimization:

```bash
npm run build
```

# Scaffolding the project

- Folders:
  - `build`: contains the optimized version of the project.
  - `node_modules`: contains all installed dependencies and its sub-dependencies.
  - `public`: static files (CSS, icons, manifest, index.html).
  - `src`: main App folder for tests, logic, routes, etc.

- Files
  - `public/index.html`: the classic html file.
  - `public/manifest.json`: it makes sure the project is [progressive web compliant (PWA)](https://en.wikipedia.org/wiki/Progressive_web_app) providing essential metadata enabling the application to work on a mobile device.
  - [public/robots.txt](https://www.robotstxt.org/robotstxt.html): used to define which pages from your React APP search engine crawlers can or cannot request.
  - `src/index.js`: renders src/App.js into public/index.html.
  - `src/App.js`: the application itself.
  - `src/App.test.js`: test cases.
  - `src/reportWebVitals.js`: metrics for user experience.
  - `src/setupTests.js`: imports testing libraries.
  - `package.json`: list all the project's dependencies (packages, libraries) required to run the application.
  
- Build helpers:
  - `Babbel`: converts react code into a version of JavaScript code the browser can understand. This happens after running `npm run build`.
  - `Webpack`: a module bundler used to make the code more performant (optimized) for the browser by dividing the project into chunks/portions of code that are located inside `build/static/js` after running `npm run build`.

# Short Notes

1. If you want to have more control over optimization, you can run [npm run eject](https://github.com/facebook/create-react-app/blob/main/packages/cra-template/template/README.md#npm-run-eject) (not recommended).
2. The browser does not understand `saas` (extension .scss) and, therefore, React will convert `.scss` down to `.css`.
3. Bootstrap 5 does not require jQuery, only JavaScript.
4. Use MaxCDN to load Bootstrap from cache for faster loading time.
5. The `package-lock.json` is automatically generated when running `npm create-react-app` and it is a good practice to NOT include this file in `.gitignore`. This file is generally committed for versioning control.
5. When using `yarn.lock`, there's no need to keep the `package-lock.json`.
