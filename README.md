# API calls


- [Axios](#axios)
  - [Fundamentals](#axios-fundamentals)
- [React query](#react-query)
  - [Client Data vs Server Data](#client-data-vs-server-data)
  - [Typical Fetching Requirements](#typical-fetching-requirements) 

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











