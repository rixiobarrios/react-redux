# Part 1: Intro to Redux

## Learning Objectives
- Explain the Three principles of Redux
- Identify how Redux leverages unidirectional data-flow and immutability
- Explain the role of the store in a React app and how it influences the architecture of a React app
- Explain the roles of actions and the reducer
- Explain what problem Redux Solves
- Talk through updating state in an app that uses Redux

# What is Redux? (0:05, 5 min)

Redux is a state management library.
It solves the problem of having a bunch of localized component states by funneling them into a central hub.
This really begins to become an issue for developers as applications scale, increasing in complexity and size.
Redux is extremely opinionated and entails writing applications with heavy limitations.

The limits Redux imposes don't necessarily restrict what you write so much as how you write it.

With the [Redux Devtools Chrome Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en), we have the ability to see and interact with the data in our programs as if it were a movie we could pause, play, rewind, and fast-forward.

**Take a moment to install the [Redux Devtools Chrome Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en)**

# Use Case (0:15, 10 min)

Let's take 5 minutes to read through a blog post written by the creator of Redux, Dan Abramov.

 From [***You Might Not Need Redux***](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367#.47e1bpmhj):

> Redux offers a tradeoff. It asks you to:
- Describe **application state as plain objects and arrays**.
- Describe **changes** in the system as **plain objects**.
- Describe **the logic for handling changes as pure functions**.

It is definitely not necessary to involve in every app.
The blog post lists use cases for Redux, features that Redux provides to the application developer, and the implementation limitations that Redux imposes.

> However, if you’re just learning React, don’t make Redux your first choice.
Instead learn to [think in React](https://facebook.github.io/react/docs/thinking-in-react.html). Come back to Redux if you find a real need for it, or if you want to try something new. But approach it with caution, just like you do with any highly opinionated tool.

For this lesson, we're throwing caution (or a regard for our own momentary sanity) to the wind. There is a formidable learning curve with Redux, but the exposure is valuable for a few reasons:

1. It is being increasingly adopted by React developers, especially teams working on very large applications.

2. Working with Redux exposes us to very elegant applications of functional programming and convey its power and potential.
Functional programming presents us with the challenge of having to think in new ways about how our programs are composed and pass data.

3. Its limitations force the developer to very carefully consider the structure of their application.

---

# Functional Programming and the React Ecosystem (0:25, 10 min)

You may hear developers talking about how functional programming is revolutionizing Javascript and wonder how this is so.
Let's revisit the concept of a pure function: given any input, a ***pure function*** will return the exact same output.
It will also ***have no side effects***.
This means that the **pure function** is *only concerned* with returning some output that is a function of its input.

Impure functions include:
  - `Math.random()`
  - `new Date().getTime()`
  - `$.ajax()`

Part of the power of React is this very idea applied to views. We pass in props a component, and we get the same predictable component every time.

In short, applying concepts of functional programming to a front-end library ensure ***a high degree of predictability***.
It also creates a modular architecture that allows a library like Redux to intervene in how React manages state.

Redux will use functional programming's approach to function composition: reusing certain functions in the construction of other functions.
Ultimately, these aggregated functions will provide the functionality of Redux.

# Concepts of Redux

## Application State &  Immutability in Redux (0:40, 15 min)

Simply put, state is a representation of your application's data. Redux manages your application's state, encapsulating the data stored in your variables, in something called **The Store**.
When using Redux, we want to treat application state in such a way that it is always ***copied*** and never directly mutated.
The end result of this approach is that we get a history of the application's states over the course of its usage.
This affords a great resource for developers debugging a complex user-interface, for example.
A developer using Redux in this way could see the exact series of user actions that produces the bug.


### Techniques for Avoiding the Mutation of State

<!-- TODO: Create immutability technique markdown reference and transfer this info there -->

ES6 has a couple nice features that make dealing with immutable data easier: `Object.assign()` and the spread operator `...`.

`Object.assign()` in particular gives us a powerful way of copying objects.

> Example:
```js
let cat = {
  name: "Meowy Mandel",
  meowy: true
}
let copiedCat = Object.assign({}, cat)
```

`...` or the spread operator gives us a nice way of combining arrays.
It exposes or "unwraps" the values in an array.

```js
let fruits = ["Tomato", "Cucumber", "Pumpkin"]
let updatedFruits = [...fruits, "Avocado"]
```

The above code is equivalent to this:

```js
let fruits = ["Tomato", "Cucumber", "Pumpkin"]
let updatedFruits = fruits.concat("Avocado")
```

For arrays of primitives, we have the good, old reliable `.slice()` (first implemented in ES3) for copying array. and spread operators for combining arrays or parts of arrays.

For arrays of objects, you can use `map()` in concert with `Object.assign()`

> Example:
```js
let todos = [
    {todo: "learn to thrash"},
    {todo: "learn redux"},
    {todo: "hang tight"},
    {todo: "stay loose"}
]
let todosCopy = todos.map(obj => Object.assign({}, obj))
```


##### Remember, `.slice()` not `.splice()`!


[Splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)
[Slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)

It's an easy mistake to make, since there is a one letter difference.

`.splice()` ***is a mutator method***, meaning it modifies whatever it is called on.

`.slice()` ***is not a mutator method***. Use it for ***copying*** all or part of an array!


## You Do: Practice with Immutable Data and Pure Functions (20 min)
> Write pure functions to complete the following without mutating state

```js
const arr = ['fish', 'bird', 'dog', 'monkey', 'turtle']
```

1. Add `cat` and `mouse` to the array
<details>
<summary> Solution </summary>
<br>
```js
const addArr = (arr) => [...arr, 'cat', 'mouse']
const addArr = (arr) => arr.concat('cat', 'mouse')
```
</details>


2. Add 'snake' to the front of the array
<details>
<summary> Solution </summary>
<br>
```js
const addFront = (arr) => ['snake', ...arr]
const addFront = (arr) => [].concat('snake', arr)
```
</details>


3. Delete 'monkey' from the array
<details>
<summary> Solution </summary>
<br>
<code>
const removeArr = (arr, animalName) => {
  let index = arr.indexOf(animalName)
  return arr.slice(0, index).concat(arr.slice(index + 1))
}
</code>
</details>

```js
const obj = {
  lastName: 'Asimov',
  occupation: 'author',
  books: ['Foundation', 'I, Robot']
}
```

4. Change occupation from 'author' to 'writer'
<details>
<summary> Solution </summary>
<br>
```js
const changeOcc = obj => Object.assign({}, obj, {occupation: 'writer'})
const changeOcc = obj => ({...obj, occupation: 'writer'})
```
</details>


5. Give the author a 'firstName' key with the value 'Isaac'
<details>
<summary> Solution </summary>
<br>
```js
const addName = obj => Object.assign({}, obj, {firstName: 'Issac'})
const addName = obj => ({...obj, firstName: 'Isaac'})
```
</details>


6. Add 'Pebble in the Sky' to the array of books
<details>
<summary> Solution </summary>
<br>
```js
const addBook = obj => ({...obj, books: obj.books.concat('Pebble in the Sky')})
```
</details>

```js
let todos = [
    {todo: "learn to thrash"},
    {todo: "learn redux"},
    {todo: "hang tight"},
    {todo: "stay loose"}
]
```


7. Give each todo a 'completed' field with value 'false'
<details>
<summary> Solution </summary>
<br>
```js
const addComplete = todos => todos.map(todo => ({...todo, completed: false}))
```
</details>

---

# Elements of Redux (1:00, 20 min)

## The Store

The store is a kind of hub that all the information (**application state**) in a program flows through. **The store** encapsulates not only the data in the program, but also controls the flow of program data, storing each change in a separate state. Redux even gives us the ability to time travel through our application's history of application states. It's like the principles of git applied to application-state rather than file-state.

All the principles of Redux are embodied in the store. The store  holds an application's **states** (including current and previous states), **actions** which specify different changes to make on some part of the application state, and **the reducer**, which specifies which actions update the state object.

Since state is being represented as an immutable data-structure, we cannot directly modify it.
Changing state in the program requires dispatching an action that modifies a copy of the state.
This becomes the next state of the program, spat out by the reducer.

Every time an action has been dispatched via the reducer, we want to update the UI. So we subscribe the render method to any changes taking places to the application's state object.


## Actions

An action is a garden-variety Javascript object that describes what kind of change is to take place, specifying what change to make to what data.

The minimum requirement for an action is that the action must have a type property that **is not** `undefined.`
<details>
  <summary>
  Unless you are using middleware, the value of `type` be a string in order for Redux's time travel features to function.
  </summary>
  Strings are preferred for values of the `type` property of an action since they can be [serialized](https://en.wikipedia.org/wiki/Serialization). String instances are simple, modular data that are stored in memory and recalled from memory in a straight-forward manner; this is not necessarily the case with more complex data types like objects, especially ones involving references.

  [This serialization is important for Redux's time travel feature.](https://github.com/reactjs/redux/blob/master/docs/faq/Actions.md#actions-string-constants)
</details>


## The Reducer

The reducer specifies how actions update the state of the application, generating the next application-state.

# A Model of the Store

This is an approximation of a store.

```js
class Store {
  constructor(){
    this.subscriptions = []
    this.state = null
  }

  getState(){
    return this.state
  }

  // the ONE FUNCTION that handle any and all updates to our application-state
  reducer(action, state){
    // decides what type of state change
    switch (action.type) {
      case 'ADD_TODO':
        return {
          id: action.id,
          text: action.text,
          completed: false
        }
      case  "TOGGLE_TODO_COMPLETED":
        return {
          ...state, //object spread operator
          completed: !state.completed
        }
      default:
        return state
    }
  }

  //called any time the reducer applies an action to a state
  subscribe(subscription){
    this.subscriptions.push(subscription)
  }
}
```

## Additional Store Methods

0. `.getState()`

  - `store.getState()`

0. `.dispatch({})`  

  - `store.dispatch({ type: "ACTION_TYPE" })`

0. `.subscribe()`

  - `store.subscribe(this.render)`

# We Do: Building a Counter in Redux (30 min)

[Building a Counter in Redux](https://git.generalassemb.ly/ga-wdi-exercises/react-redux-counter)
