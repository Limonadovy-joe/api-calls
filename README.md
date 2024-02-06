# API calls


- [Axios](#axios)
  - [Fundamentals](#axios-fundamentals)
- [React query](#react-query)
  - [Client Data vs Server Data](#client-data-vs-server-data)
  - [Typical Fetching Requirements](#typical-fetching-requirements)
  - [Query Keys](#query-keys)
  - [Parallel and Dependent, Deferred Queries](#parallel-and-dependent-deferred-queries)
  - [Cache States And React Query DevTools](#cache-states-and-react-query-devTools)
  - [Error handling](#error-handling)

## Axios
Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.Since launching this course, we've changed where the React Query package is located. Before, it was under the react-query package. Now, it's under the @tanstack/react-query package.

This affects how you'll install and import the package throughout the course, but the actual API remains the same.

## React query
- Back in the days before JavaScript. All the data related to the request would be compiled into a static HTML document, and this document would be sent back to the browser. Any interaction that updated or fetched new data would trigger this **round-trip process from the server to the browser**
- This all changed with the introduction of **AJAX** in Gmail in 2005. Suddenly, a webpage could request more data without reloading the page
- Nowadays, this kind of API interaction is more or less required for any app that shows user-generated content
- Many developers think **they can treat the data that comes from their server the same as the state they store in the client.** However, there are important differences between **server state and client state** that affect how we should handle these.

### Client Data vs Server Data
**Client state:**</br>
- When we talk about client state, we're talking about state that **is only stored in the browser.** It could be stored using the **useState or useReducer hooks** or a third-party state management library like Redux or MobX.
- This kind of state is great for **storing UI state**, like whether a menu or dropdown is open, whether dark mode is turned on, or the user input in a form before it's sent to the server.

**Server state:**</br>
- Because server state is **stored remotely** and any user can change it, any data that comes from our server could potentially become out of date, even after only a few seconds, so we should treat it with care.

### Typical Fetching Requirements
1. Rendering the same data across multiple components **without doing re-fetches** - De-duplicating identical requests
2. **Using a cache to limit the number of 'fetch' requests**
3. Automatically refetching to have the freshest data
4. Handling pagination
5. Updating our local data when we make mutations to the remote data
6. **Orchestrating requests** that depend on the result of other requests

A lot of React data fetching patterns and libraries take advantage of global state managers, like Redux or MobX to solve many of these problems. These libraries work fine, but **they often introduce more problems by treating server state the same as client state and using a global store.** What we need is something specially suited for **handling server state - something that automatically handles error and loading states for us.**

This is exactly why React Query was created.


### Query Keys
- After React Query makes a query, **it places the result into a cache**. This makes it possible for the **same query data to be used multiple times** without making duplicate requests
- Each item of the query key array **should cause the query to refetch when it changes**

**Simple Query Keys:** </br>
- The simplest form of a key is an array with constants values, which is great for **Generic List - index resources  or Non-hierarchical resources - that don't need extra parameters**
```ts
useQuery(["labels"], fetchLabels);
useQuery(["users"], fetchUsers);
useQuery(["issues"], fetchIssues);

// A list of todos
useQuery({ queryKey: ['todos'], ... })

// Something else, whatever!
useQuery({ queryKey: ['something', 'special'], ... })
```

**Array Keys with variables:** </br>
- When a **query needs more information to uniquely describe its data**, you can use an array with a string and **any number of serializable objects to describe it: Hierarchical or nested resources, Queries with additional parameters**
- we can use the different parts of our query key into separate items, like any parameters, IDs, indices - anything that our query depends on
```ts
useQuery(["users", 1], fetchUser);
useQuery(["labels", labelName], fetchLabel);
useQuery(["issues", {completed: false}], fetchIssues);
```
There's no one right way to write a query key:  **start generic, then go more specific**


### Parallel and Dependent, Deferred Queries
- "Parallel" queries are queries that are executed in parallel, or at the same time so as to maximize fetching concurrency
- If you can't decide whether to use multiple useQuery hooks or put all of your requests in the same query function: **think about whether you need a separate cache, loading, and error state for each query, or whether its better for them to be combined**
- useQuries - It's especially useful when the number queries you are fetching can dynamically change.

**Dynamic Parallel Queries:** </br>
- If the number of queries you need to execute is changing from render to render, you cannot use manual querying since that would violate the rules of hooks
```tsx
function App({ users }) {
  const userQueries = useQueries({
    queries: users.map((user) => {
      return {
        queryKey: ['user', user.id],
        queryFn: () => fetchUserById(user.id),
      }
    }),
  })
}
```

**Dependent Queries:** </br>
- Dependent (or serial) queries depend on previous ones to finish before they can execute. 
- Dependent queries by definition **constitutes a form of request waterfall**, which hurts performance.
- If you can, it's always better to **restructure the backend APIs** so that **both queries can be fetched in parallel**, though that might not always be practically feasible
- In the example above, instead of first fetching **getUserByEmail** to be able to **getProjectsByUser**, introducing a new **getProjectsByUserEmail** query would flatten the waterfall.


**Deffered queries:** </br>
- One would be to use a **debounce function, which would wait a bit after the last keystroke before making the request.**
- To keep things simple though, we'll just wrap our **input in a form and only make a new request when that form is submitted.**


### Cache States And React Query DevTools
- remember, the purpose of React Query is to **keep track of our server state**. This is the state that lives remotely on the server and could change at any time
- Once we have data in our cache, we can show that data to the user. **But what happens when data changes on the server?**
- React Query will automatically refetch our data, but how does it know when to do that?
- **idle** means the query doesn't need to be fetched, **fetching** means it is currently being fetched, and **paused** means the query tried to fetch, but was stopped for some reason - usually because the network is offline.
- Note that the **fetching state** is different from the **loading state**. **A query only has the loading state the first time it loads and there's no data**, while the **fetching state is used by the query cache any time a query is refetched**, including the first time.**
- the **cache entry** could be set to either **fresh, meaning the data on the server is unlikely to change soon**, or **stale, meaning the data on the client might already be out of date with the server**

- **Finally, when every component that uses a query has unmounted**, the cache entry for **that query is marked as inactive**. The data is kept just in case the query is used again, but React Query might remove that entry from the cache if it isn't used for a while.

**Stale vs Fresh Data:** </br>
- If the query is stale, it'll get refetched.  
```tsx
const userQuery = useQuery(
  ["user", username],
  () =>
    fetch(`https://api.github.com/users/${username}`)
    .then(res => res.json()),
  { staleTime: 1000 * 60 }
);
```
- Once 60 seconds have passed since this query was last fetched, React Query will change its state to stale, which means it's eligible to be refetched once again.

**Triggering a Refetch:** </br>
- Of course, we already know React Query will fetch when the component first mounts or when the query key changes.
- The second refetch trigger is when the user re-focuses on the browser window
- The next refetch trigger is network reconnections.
- And finally, there is an optional timed refetch trigger which we can configure. This is activated by setting the refetchInterval. This option is best when you have **frequently changing data** and want to always keep the cache up to date.


**Clearing the cache:** </br>
- By default, when a query has been inactive for more than **5 minutes**, React Query clears it out.
- What happens when a query is removed from the cache? That query will behave as if the page had been reloaded completely. The user will see the loading state until the query resolves, and then they'll see the query data.


### Error handling
- One area this becomes a little tricky is for fetch requests. There are very few circumstances in which a fetch will reject. For example, an **HTTP 500 error, which is a server error, won't cause fetch to reject.** That means we have to take care of rejecting the promise ourselves.

**Error retries:** </br>
- This is actually the default behavior of React Query. When a query fails to refetch, **it will try three times to fetch it again,** with a delay between each attempt. If the query still fails, it will display the error to the user.
- **This only applies to refetches**. If a query fails to fetch when it first loads, it will immediately display the error to the user without retrying.
- There's just one more thing to cover about error handling: **What happens when a query fails but there is already data in the cache?**
- If the data is still **in the cache**, **there's no reason we can't just keep showing the user that stale data** - it's better than only showing them an error. In fact, if we reorganize our component the right way, we can show both the error and the stale data.
- The important thing to remember is that any data that's still in the cache is available, even if a later query had an error.



