<div align='center'>
  <h1>Apollo vs Redux</h1>
</div>
  
# Table of Contents

- [Redux](#redux)
- [Apollo](#apollo)
  - [Apollo Client](#apollo-client)
  - [Apollo Server](#apollo-server)
  - [Apollo Studio](#apollo-studio)
  - [Apollo Federation](#apollo-federation)

# Redux

[Redux](https://redux.js.org/tutorials/fundamentals/part-1-overview) is a pattern and a JavaScript [state](https://reactnative.dev/docs/next/state) management library that serves as a centralized store (single source of truth) to manage and store the `global state` and data flow across an entire application. 

Redux use a `reducer` to update a state in response to an action (a JavaScript object that describes an event). A reducer is just a function that receives `state and action` as arguments and returns an object with the shape of the data to be stored. To utilize the reducer in a react component, the `useReducer` hook is needed:

Example applying a Reducer to a React Context API.

```javascript
import React, { createContext, useReducer } from 'react';

// Hash table to store user action types:
export const USER_ACTION_TYPES = {
  SET_CURRENT_USER: 'SET_CURRENT_USER',
};

// Reducer implementation:
const initialState = { currentUser: null };

const userReducer = (state = initialState, action) => {
  console.log('Dispatched!');
  const { type, payload } = action;

  switch (type) {
    case USER_ACTION_TYPES.SET_CURRENT_USER:
      return {
        ...state,
        currentUser: payload,
      };
    default:
      throw new Error(`Unhandled type ${type} in userReducer`);
  }
};

// Create the UserContext with a default initial value. This is a good practice to ensure the component receives a fallback when consuming the context outside its provider (when the child component is not being wrapped in a UserProvider).
export const UserContext = createContext({
  setCurrentUser: () => null,
  currentUser: null,
});

// Create the UserProvider component:
export const UserProvider = ({ children }) => {
  // Using the useReducer hook
  const [state, dispatch] = useReducer(userReducer, initialState);

  // Destructure the current user from state:
  const { currentUser } = state;
  
  // Function to set the current user
  const setCurrentUser = (user) => {
    dispatch({
      type: USER_ACTION_TYPES.SET_CURRENT_USER,
      payload: user,
    });
  };

  return (
    // Provide the state and dispatch function to context consumers:
    <UserContext.Provider value={{ currentUser, setCurrentUser }}>
      {children} {/* Render children components inside the provider */}
    </UserContext.Provider>
  );
};

```

If the app is small or simple, Redux might not be needed, as React's built-in hooks for state management (such as useState and useReducer) may suffice. React's Context API with createContext and useContext can also be used. But for larger applications, Redux can provide significant advantages.

- `State management`: in React, it involves keeping track of dynamic data that triggers the rendering of components, such as user inputs, API responses, or other dynamic information (theme, translation, etc.). The `useState` hook is a prime example of React's built-in state management. It allows functional components to have their own local state, enabling management of dynamic data specific to that component.

# Apollo

[Apollo](https://master--apollo-docs-index.netlify.app/docs/intro/platform/) is a platform for building scalable, production-ready GraphQL APIs. It offers out-of-the-box features, a client-side library ([Apollo Client](https://www.apollographql.com/docs/react/why-apollo)), a server-side library ([Apollo Server](https://www.apollographql.com/docs/apollo-server)), a cloud-based suite of tools ([Apollo Studio](https://master--apollo-docs-index.netlify.app/docs/intro/platform/#:~:text=In%20addition%20to%20its%20open,together%20known%20as%20Apollo%20Studio)), and support for creating a distributed GraphQL schema ([Apollo Federation](https://master--apollo-docs-index.netlify.app/docs/intro/platform/#4-federate-your-graph-with-apollo-federation)).

## [Apollo Client](https://www.apollographql.com/docs/react/why-apollo)

Apollo Client is a state management JavaScript GraphQL client-side library used to facilitate the interaction between the client-side (frontend) and the server-side (backend) of applications using GraphQL

Its capabilities include `managing both local and remote states` of the application. It handles GraphQL data `caching` for both remote and local state management replacing traditional state management libraries such as Redux. For remote data, it handles and caches the results of GraphQL queries (data fetching). For local state, it stores and manages client-side data. All within the same unified caching system.

- Reactive Updates: when data in the cache changes (whether it is local or remote), Apollo Client automatically updates the React components from the UI.

- Local state: data is stored and managed directly within the client application or component memory (local storage). It does not require network requests to fetch and update data by the client. Data is not persistent beyond the user's session (data is lost after logout). Access is immediately available and fast to retrieve.

- Remote state: data is stored and managed on the server, typically in a database. It requires network requests (e.g., using GraphQL or REST APIs) to fetch and update data by the client. Data is persistent and shared among multiple clients. Data may not be immediately available. Requires a mechanism to keep local and server data in sync.

![apollo](https://github.com/user-attachments/assets/a7d5afd6-0288-4634-8e2e-2a5ac385b066)

## [Apollo Server](https://www.apollographql.com/docs/apollo-server)

Apollo Server is a JavaScript/TypeScript GraphQL server-side library for building scalable and production-ready GraphQL servers in Node.js. It has support for schema stitching, real-time subscriptions, custom directives, and Node.js middlewares.

## [Apollo Studio](https://www.apollographql.com/tutorials/fullstack-quickstart/06-connecting-graphs-to-apollo-studio#:~:text=Apollo%20Studio%20is%20a%20cloud,this%20tutorial%20are%20free%20features)

Is a cloud-based suite of tools with support for prototyping, performance monitoring, and deployment.

## [Apollo Federation](https://master--apollo-docs-index.netlify.app/docs/intro/platform/#4-federate-your-graph-with-apollo-federation)

Is an architecture used for building distributed GraphQL schema, i.e., to separate the GraphQL API into individual/dedicated services according to their functionality. This federated architecture is akin to a microservice architecture. Useful for systems that scale, allowing teams to work independently on different parts of the API.