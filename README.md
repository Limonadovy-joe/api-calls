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
  - [Using The Query Client](#using-the-query-client)
  - [Manual Query Invalidation](#manual-query-invalidation)
  - [Query Filters](#query-filters)
  - [Query Cancellation](#query-cancellation)
  - [Fetching States](#fetching-states)
  - [Prefetching queries](#prefetching-queries)
  - [Mutations](#mutations)
  - [Optimistic Updates](#optimistic-updates)
  - [Paginated Queries](#paginated-queries)
  - [Infinite queries](#infinite-queries)
  - [Realtime updates with websockets](#realtime-updates-with-websockets)
  - [Suspense Mode](#suspense-mode)
  - [Render Optimization](#render-optimization)

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
```tsx
useQuery(["users", 1], fetchUser);
useQuery(["labels", labelName], fetchLabel);
useQuery(["issues", {completed: false}], fetchIssues);
```
There's no one right way to write a query key:  **start generic, then go more specific**

**EXAMPLE QUERY KEYS**:</br>
```tsx
async function fetchIssues(status) {
  let url = new URL("https://ui.dev/api/courses/react-query/issues");
  if (status) {
    url.searchParams.append("status", status);
  }
  return fetch(url.toString()).then((res) => res.json());
}

function Issues() {
  const [status, setStatus] = React.useState("");

  const issuesQuery = useQuery(["issues", { status }], () =>
    fetchIssues(status)
  );
  const issues = issuesQuery.data;
  return (
    <>
      <h2>Issues{issuesQuery.isFetching ? "..." : ""}</h2>
      <select
        name="status"
        value={status}
        onChange={(event) => setStatus(event.target.value)}
      >
        <option value="">All issues</option>
        <option value="backlog">Backlog</option>
        <option value="todo">Todo</option>
        <option value="inProgress">In Progress</option>
        <option value="done">Done</option>
        <option value="cancelled">Cancelled</option>
      </select>
      {issuesQuery.isLoading ? (
        <p>Loading...</p>
      ) : (
        <ul>
          {issues.map((issue) => (
            <li key={issue.id}>{issue.title}</li>
          ))}
        </ul>
      )}
    </>
  );
}
```

**I didn't really understand why did you put the status as an object on qureyKey?**
- In this case, the object seems to be redundant as we only have one filter. In a real-world application, you would have multiple filters, and grouping them into an object helps to preserve the query key by **"ignoring" the order of the filters**.
- This query key ["issues", label, status] is different than this ["issues", status, label] but this **one ["issues", { label, status }] will be similar to this ["issues", { status, label }].**
- in the future, if u get a requirement to only retrieve a single issue with its id. Then you would have to keep the key as ["issues", issueId]. Always have filters in an object. **The direct entries into the array are mostly the ones that would end up in the URL [as path params]** if one were to follow the **REST pattern**.






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


### Using The Query Client
- React Query puts control in your hands as the developer. You can configure the settings for each query directly, like staleTime, cacheTime, and retry, or you can use the default settings that come with React Query

There's one more default you can configure on the Query Client - the default query function.

**Imperative Query Fetching:** </br>
- The useQuery hook is strictly declarative.
- This is the behavior you most often want, but there might be a time when you want to **trigger a query imperatively**. For example, you might want to perform a search, grab the top result, and immediately trigger a navigation to the relevant page using React Router's navigate function without displaying results.


### Manual Query Invalidation
- React Query will only ever **refetch a query if it is stale**, but by default all queries are stale once their results come back. Queries with the **staleTime option will remain fresh until the staleTime has passed.**
- There are three things which will cause React Query to automatically refetch any stale queries: Any time a component that has the useQuery hook mounts, whenever the user focuses the browser window, or whenever the network goes offline and then comes back online.

However, there are times you might know for a fact that the data has changed and should be updated.

**Invalidating Queries:** </br>
- Most of the time, we just want to mark queries as stale and let React Query handle the refetching itself.

**Refetching vs Invalidating:** </br>
- **queryClient.refetchQueries** will force any queries that match the provided query key to refetch. This includes active, inactive, fresh, and stale queries. Yes, even those inactive queries that are just sitting in the cache not doing anything will be refetched.
- **queryClient.invalidateQueries**, on the other hand, will only mark any fresh queries as stale, which automatically triggers a refetch. However, since React Query will never automatically refetch inactive queries, queryClient.invalidateQueries results in fewer queries refetching, which means less network traffic.


### Query Filters
- Remember, queries in the cache have several states they can be in. fresh is used for queries that likely won't change, stale is for queries that should be refetched soon, and fetching is for queries that are currently being fetched. Queries are also considered active if they have at least one subscriber, meaning a mounted component using the useQuery hook, and inactive if it has no subscribers.


### Query Cancellation
- Every network request and every computation has some kind of cost. Some users feel that cost more than others, especially folks with metered internet connections or low-powered devices. If you want your app to be respectful to users, you need to consider how to keep these costs as low as possible.
- React Query only refetches stale data based on signals from the user. If you want to fetch data less frequently, you can set the query staleTime to keep data fresh for longer.

- Most of the time React Query will cancel the query automatically. For example, **the component might unmount before the query resolves**, or the query key might change mid-request, making the data unusable.
- And any work that the CPU doesn't have to do means a longer battery life for your mobile users.

**When does a React Query signal a query to cancel?**
- When the component is unmounted while a query is fetching
- Manually, using the queryClient.cancelQueries
- when the query key changes while a query is fetching


### Fetching States
- Nobody likes a loading spinner, but it's better than not knowing whether or not data is being loaded. That's why React Query gives us access to the **isLoading state so we can show our users if a query is being loaded for the first time.** After that, React Query stores the data in the cache so we don't have to show that loading state again.
- React Query provides that refetching state with the **isFetching property which lets you show that the data is being refreshed in the background.**

**The useIsFetching() hook returns a number indicating the number of queries across the entire app that are refetching.**



### Prefetching queries
- Seeing a loading state on a site is pretty commonplace these days. **Users have come to expect a little spinner or skeleton UI while the data fetches in the background.** And many loading states, especially those that show when an app first loads, are mostly unavoidable.
- **We can take advantage of this behavior to load data into the cache without needing to show a loading indicator at all.**

**Placeholder Data:** </br>
- When the query function resolves, the placeholder data will be replaced with the real data.
- You should use **placeholderData whenever you want to provide fake data to a query.** For example, a query could fetch a user's profile, and we could provide a placeholder image for their avatar.

```tsx
const UserAvatar = () => {
  const userQuery = useQuery(
    ["user"],
    fetchUser,
    {
      placeholderData: {
        avatar_url: "https://placekitten.com/g/200/200",
      }
    }
  )

  return (
    <img src={userQuery.data.avatar_url} alt="Avatar" />
  )
}
```
- This gives us something to show the user while the real data loads in the background.

Can you think of a scenario where this could go wrong though? What about with dependent queries?
- We wouldn't want the **dependent query to be using placeholder data.** To avoid this, we can check the isPlaceholderData property of our query object to see if our data is real or not.
```tsx
const userQuery = useQuery(
  ["user"],
  fetchUser,
)
const userIssues = useQuery(
  ["userIssues", userQuery.data.login],
  fetchUserIssues,
  {
    enabled: !userQuery.isPlaceholderData 
      && userQuery.data?.login,
  }
)
```

**Initial Data:**
- If placeholderData is useful for fake data, initialData is most valuable for real data - that is, **data which is very likely to be similar to the data on the server.**

**Initial Data from Other Queries:**
- **A common pattern in apps is to have an index page which shows a list of items and then a separate page which shows an item detail.** For example, Github displays a repo's issues in a list and clicking on an issue takes you to a more detailed page. Here's what the query for the index page might look like:
```tsx
const issuesListQuery = useQuery(
  ["issues", repo, owner],
  fetchIssues
);
```
- And the query for the detail page might look like this:
```tsx
const issueDetailQuery = useQuery(
  ["issue", repo, owner, issueNumber],
  fetchIssue
);
```
- An interesting thing about the Github Issues API is that the issue list API **includes most of the data about the issues, including the title, body, state, and labels.** This is actually enough data for us to render most of the issues detail page. We just need a way to pull that data out of the cache and use it as our initial data.

**Preemptive Data:**
- If we know that a **query can provide the initial data for another query**, **we can preemptively put the needed data into the cache when the first query loads.**
-  Then, when the second query loads, **it will automatically have data from the cache**, without any extra configuration on our part.
- The best place to call this is inside our query function, after the data has been loaded.
- React Query will use the moment that **queryClient.setQueryData is called as the timestamp for dataUpdatedAt.**

**Query Prefetching:**
- Since the **loading is based on user actions,** we can use those **same actions as signals to know what data the user will need soon.**
- For example, we can watch for **when the user's cursor hovers over a link that will take them to a new page, and prefetch the query that is on the other page.** Or, as an example, **we know from analytics data that when a user visits the /dashboard page, they are most likely to navigate to the /billing page.** Knowing that, we can prefetch the billingQuery so the data is ready when the user navigates.
- Adding an onMouseEnter event handler will let us prefetch data when the user's cursor rests on the link.
```tsx
const Navigation = () => {
  const queryClient = useQueryClient();
  return (
    <>
      <Link 
        to="/dashboard"
        onMouseEnter={() => {
          queryClient.prefetchQuery(
            ["dashboard"],
            fetchDashboard
          );
        }}>
        Dashboard
      </Link>
      <Link 
        to="/billing"
        onMouseEnter={() => {
          queryClient.prefetchQuery(
            ["billing"], 
            fetchBilling
          );
        }}>
        Billing
      </Link>
      <Link 
        to="/profile"
        onMouseEnter={() => {
          queryClient.prefetchQuery(
            ["user"], 
            fetchUser
          );
        }}>
        Profile
      </Link>
      <Link to="/settings">Settings</Link>
      <Link to="/logout">Logout</Link>
  </>
  )
}
```
Hopefully, this helps those pages load just a little bit faster, since the query will have a bit more time to load. And it also respects the state of the data already in the cache. **If we try to prefetch data that is fresh in the cache, React Query will just ignore the prefetch request.**


Let's also look at how the page-based query prefetching might work. For this, we'll call **queryClient.prefetchQuery in a useEffect hook to prefetch the data for the /billing page.**
```tsx
const Dashboard = () => {
  const queryClient = useQueryClient();

  useEffect(() => {
    queryClient.prefetchQuery(["billing"], fetchBilling);
  }, [])
  
  // ...
}
```
- There are a few things to beware here. First, this technique only works if we have all of the variables that the query depends on. The examples I use above use string constants as the query key, but it might be tricker to prefetch if we have to use several variables in our query key or query function.
- **Also, this couples the queries to the places where those queries are prefetched. Any changes to either the query key or query function have to be made in both places.**
- The best way to avoid this problem is to define **your query function separately from where it is used and then import it both where the query data is prefetched and the page where the data is actually used.** You can even write a function called a "query key factory" which takes parameters and provides you with the correct query key.


### Mutations
- the useMutation hook lets you manage the state of mutations sent to the server. This hook allows you to perform mutations on your server state by triggering side effects.
- A mutation is any function that causes a side effect - most commonly changing the state of some data outside of the function.
- And, by definition, **any HTTP call to a server that changes state is considered a side effect.** That HTTP call mutated the state on the server, which means future responses from the server might have different results.

**useMutation vs useQuery:**
- **The first and most obvious difference is the mutate method, which is what you call to fire off the mutation.** You see, useQuery runs declaratively. As soon as the component mounts, it fetches the data. But we don't want that to happen for mutations.
- Sending the mutation by calling a function makes it really handy for **use in event handlers or useEffect calls.**
-  When the isSuccess state is truthy, **we display the updated name that was returned by the mutation response.** Of course, **the user likely doesn't want to see this state forever.** It's mostly there to serve as an indication that the mutation was a success. **Eventually, we would want to reset the mutation and clear the data that was returned.**

**Mutation Resets:**
- To help us reset the mutation state, the object returned by useMutation includes a reset method. **Calling this will clear out the state of our mutation and set it back to its initial state.**
- To start the timer, we'll take advantage of the mutations lifecycle methods, which are the same that useQuery has: **onSuccess, onError, and onSettled. There is one addition which is called before the mutation function is fired: onMutate**
- The second difference between useMutation and useQuery is in the parameters - **you don't have to pass a query mutation key, just the mutation function.** This implies another difference - **mutation results aren't cached like query results are.** If you make a mutation in one component, you won't be able to access the response data in another component without sending it some other way.
- React Query makes no assumptions about what your data returns, **which means you are responsible for making sure queries are updated when the mutation response comes back. **

**Direct Cache Updates:**
- **Depending on the return value of your API, you might be able to directly update the cache entry for any queries that would be changed by your mutation.**
- We can once again use the onSuccess callback along with queryClient.setQueryData to do this.
- This is a simple and fast option, but any time we're manually updating the cache, we run the risk of introducing bad data.
- **The more cautious and resilient approach would be to invalidate the queries that were affected by the mutation and have them refetch.**




### Optimistic Updates
- Users want snappy UIs, and that's a problem when making mutations. If we have enough data and **can guess what the server response will be, we can update our cache (and our UI at the same time) with fake data while the server is still working.** Then, **when the server responds, we can update the cache with the real data. We call this technique "Optimistic Updates"**
- Let's create a mutation to add a comment to a Github issue, with an optimistic update that adds a record to the cache immediately .
- **Our mutation now updates the queries as with real data as quickly as they can**, but with optimistic updates we can update the UI even faster with fake data.
- There's another problem with this. What happens if the mutation fails? **That would leave our fake comment in the cache, even though it doesn't exist on the server.** We'll address both of these problems by creating a **rollback, which lets us restore the cache to its pre-mutation state.**

### Paginated Queries
- Other APIs might give you access to thousands of records, each with kilobytes of data. A single issue record from the Github API is 3kb all on its own, and some repos like facebook/react have over 10,000 issues. That's a lot of data to return in a single request.
- That's why it's common for APIs to break up the data into multiple pages. Clients are expected to request data a page at a time, making additional requests when they want to get another page.

- Except you might notice a small issue - whenever you click a button to change the page, the current page's data will disappear and be replaced by the loading state.
- What we really need is some way to keep the current page's data around while the next page is loading. React Query lets us enable this ability with the **keepPreviousData option.**
- Use **isPreviousData** any time you need to check to see if you are working with the latest data that matches your query or if React Query is showing you data from a previous query.

**Prefetching Pagination Queries:**
- If you were paying attention earlier in the course, you might already know one way we can improve this experience. If there's a next page, it's very likely the user will want to navigate to it. We can prefetch the next page automatically, which will load it into the cache and make the transition instant when the user clicks the "Next" button.








**Page Numbers vs Page Cursors:**
-  On the backend, this works by jumping to the perPage * (page - 1) record, and sending us the next perPage number of records. We call the number of the first record on the page the "offset". So for page 1 with a page count of 10, it starts at the first record and gives us 10 records. For page 4, it starts at record 31 and gives the next 10 records.
- Using an API with cursors, the first request doesn't have any cursor, and the server sends back the first page of results along with the cursor for the next page. When the user wants to go to the next page, they send the cursor they got with the previous request, which returns that page and another cursor, as long as there are more records.
- Using cursors is a little bit more difficult to implement, since you have to store the previous cursors in state to use the previous button. But React Query works just as well with cursors as it does with page offsets.


### Infinite queries
- We can easily create paginated queries with React Query by including the page number in our query key and changing that value when the user goes to the next or previous page. This makes sense when we have a few pages of data that the user wants to page back and forth through.
- Another common pattern these days, popularized by Facebook, Pinterest, and Instagram, is to treat the data as an **infinite page**, **where new data either loads as the user scrolls to the bottom or is loaded when the user clicks a "Show More" button.** The data is no longer displayed as discrete pages; instead, it's one infinite page of data and components that extends forever, or at least until there is data to load.



### Realtime updates with websockets
- What are the benefits of using Websockets with React Query?
  - **notifications about updates to data can be sent directly to the client instead of needing to poll**
- Why is it important to set a **staleTime** on any queries that are updated with Websockets?
  - **The websocket will update or invalidate the query anyway, so React Query automatic refetches are redudant**


### Suspense Mode
- **Suspense is React's way of coordinating loading states for asynchronous operations**, and it's commonly used with components that fetch data or lazy load with React.lazy. React Query has built-in support for Suspense by adding a configuration option to useQuery.
- **We wrap our data loading components in a Suspense component and add the {suspense: true} configuration option to useQuery.** Then, when useQuery is loading, **the hook sends a signal to React that suspends that component. React will stop rendering the component and the nearest parent Suspense component will render a fallback component until the data is available.**


**Parallel Queries:**
```tsx
const UserAndGists = ({userId}) => {
  const userQuery = useQuery(
    ["user", userId],
    () => fetchUser(userId),
    {suspense: true}
  );
  const gistsQuery = useQuery(
    ["gists", userId],
    () => fetchGists(userId),
    {suspense: true}
  );

  return (
    <div>
      {/* render the data ... */}
    </div>
  );
}
```
- It might be hard notice from here, but if you were watch the React Query Devtools as this component mounted, you would see that the ["user", userId] query would appear first, but the ["gists", userId] query wouldn't appear until after the ["user", userId] query had loaded.
- **In other words, using Suspense mode makes these queries run in series, and you have to wait for both to load before the component finally loads.** Sometimes this is what you want, but often you want to run these queries in parallel.
- We already saw the first solution above: put both queries into separate components and wrap both of them in <Suspense>. This is the recommended solution for any apps that use Suspense, since Suspense leans so heavily on React component composition.



### Render Optimization
- Decreasing Render Count
```tsx
async function fetchId() {
  await new Promise((resolve) => setTimeout(resolve, 500));
  return {
    id: "static ID",
    number: Math.random()
  }
}

const DisplayId = () => {
  const idQuery = useQuery(
    ["id"],
    fetchId,
    {
      refetchInterval: 1000,
    });

  return <div>ID: {idQuery.data?.id}</div>
}
```
- **React Query automatically tracks the properties on the query object that you use in the component. Since we aren't using the isFetching property, React Query isn't triggering a render when that changes.**
- To help us address this, **React Query gives us the select option.** This lets us pass a function to our query which takes the data returned by the query and **lets us transform it before we use it in the component.** React Query will do a deep equals check to see if what we return from the select function matches the previous data. If it does, React Query will not re-render the component.


 

