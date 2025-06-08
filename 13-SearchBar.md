Now that you know Shadcn and Databases, its time to learn how to make and customize the coolest component today: the search bar.

But before that there are few more concepts to learn:

# 1. Normal Search Bar

# 2. Debouncing

Debouncing is a technique used to limit how often a function is executed. For example, if you have a search input that triggers an API call on every keystroke, typing "apple" would make 5 separate API calls. With debouncing, only one call is made after the user stops typing for a set delay (e.g., 300ms). This is vital when you type in a search bar/ input/ textarea, and wish autosuggestion from a database, but you don't want to search after every letter, but only after you pause typing for, say, 300ms.

## I. Debouncing Approaches

There are 3 main approaches to debounce a function:

### 1. Using a Custom Debounce Function

This can work in any JS program, not just Reactjs. Here, you create a reusable function `const debounced-fn = debounce(fn, delay)` which debounces the function `fn` till `delay` milliseconds, and returns a new function `debounced-fn` which you can use in your event-handler like `onChange`

Let's understand it with an example:

```js
const debouncedSearch = debounce(actualSearch, 300);
<input onChange={e => debouncedSearch(e.target.value)} />
```
Here, the debounced function is called on every keystroke, but the actual search only runs after the user stops typing for 300ms.


### 2. Using `useEffect` and `setTimeout`

Firstly note that `setTimeout` and `clearTimeout` are standard JavaScript functions, built into the browser and Node.js. `setTimeout(fn, delay)` schedules `fn` to run after `delay` milliseconds, while `clearTimeout(id)` cancels a scheduled timeout. 

This appears similar to Approach 1, but its better, because you can reset the timer everytime the typist prematurely starts typing in the search box. However this premature typing can only be captured in a `useeffect` hook which necessitates keeping all the code within a `useEffect` body like the following example:

```js
useEffect(() => {
  const timer = setTimeout(() => {
    // Do the search
  }, 300);
  return () => clearTimeout(timer);
}, [query]);
```

Here, you use `useEffect` to watch for changes (like `query`). Inside the effect, you set a timer (`setTimeout`) to run your function after a delay. If the effect runs again before the timer finishes (because the user typed again), the cleanup function (`clearTimeout`) cancels the previous timer. Only after the user stops typing for the delay does the function run.

Note that because this approach uses `useEffect` hook, it has to be used within a `React` component, that too a `client` component if you're working in Nextjs.



### 3. Using a Library (e.g., lodash.debounce or use-debounce)

```js
import debounce from 'lodash.debounce';

const handleChange = debounce((value) => {
  // Do something with value
}, 300);

<input onChange={e => handleChange(e.target.value)} />
```

Or, in Next.js, you can use the use-debounce package for hooks-based debouncing

---
NB. : *I personally prefer the 2nd Approach*



## II. Abort Controller

When using debouncing, especially in a search bar, you might quickly fire off several fetch requests (one for each keystroke).
But you only care about the latest request—the others are now outdated. If you don’t cancel the old requests: They might still complete and update your UI with stale data.

First you should know that there's a `AbortController API` built into all modern browsers. Assuming the 2nd (useEffect + setTimeout approach), for this, you add the following lines at the beginning of your `useEffect` body :

```js
const controller = new AbortController();
const { signal } = controller;
```

Then in addition to the existing `clearTimeout`, you add another line to the return() function body :
```js
controller.abort(); // <-- This cancels the previous fetch if it's still running!
```

You also pass the `signal` object to fetch (as in `fetch(url, { signal })`), which allows that fetch request to "listen" for an abort event







