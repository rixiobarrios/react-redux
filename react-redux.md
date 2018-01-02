# Part 2: React and Redux

## Learning Objectives
- Explain how Redux is useful in a React app for managing state
- Explain how Redux fits in to a React app
- Setup a React app to use Redux
- Dispatch state changes from within React components

## Framing
We previously covered Redux at a high level, how it works and why it's useful in building applications with React where we're managing a lot of state.

We're going to review Redux, build out an example using more complex state, then integrate our working state management with some prebuilt React components.

## Review: Redux

What are the three parts of Redux? How do they work?

<details>
	<summary>**3 Parts of Redux**</summary>
	
1. Warehouse (Store)
2. Robot Minions (Reducers)
3. Invoices (Actions)

Redux works a lot like Amazon does. There's this massive warehouse (the store) with all this stuff in it. This warehouse is way to large for poor ol' Jeff Bezos to run by himself so, being the eccentric billionaire that he is, he's built a horde or Robot Minions (reducers) to manage it for him. Each Robot Minion is in charge of some part of the warehouse. Whenever someone needs something from the warehouse an Invoice (action) is sent to the warehouse, to the appropriate Robot Minion, who then carries out the order.

That's not really how Redux works, but kind of. All our state is stored in a single place (the warehouse). Whenever we want to update our state, we do so with a reducer (Robot Minion). The reducer needs to know what operation you're asking it to perform on the current state and any new data that should become part of state, which it does through a object literal called an action (Invoice).

</details>

## Redux

We are going to build out a simple warehouse inventory management tool using Redux and React over the course of this lesson. We will start by building out the Redux functionality for review and then integrating it with our React components.

### We Do: React Redux Warehouse

Clone down [this repository](https://git.generalassemb.ly/ga-wdi-exercises/react-redux) for the exercise and run `npm install`. 

- build out a warehouse inventory management system with just redux
- subscribe to store updates with `console.log()` of state
- write out constants, action creators, reducers, etc
- dispatch actions in `index.js` to see state updates in the console

### You Do: Review the Redux solution branch

Checkout the `solution-redux` branch. Explore the codebase and review the following questions:

- Where are the actions?  
- Where is the store?
- Where are the reducers?
- How is the reducer structured?

When you're finished, checkout the `master` branch.

### We Do: Project setup

First we need to build out the project structure:

```sh
mkdir actions constants reducers
touch actions/order.js constants/order.js reducers/order.js store.js
```

We're creating three directories: `actions/`, `constants/`, and `reducers/`. The `actions/` directory is where we'll build out our action creators - functions that return an object literal representing a redux action. The `reducers/` directory is where we'll build out our reducers - functions that take our current state and an action and return a new, updated state object. The `constants/` directory is where we'll define constant variables representing our action types.

We're grouping related functionality by filename: `order.js`. Splitting functionality in this way is common but comes with a lot of overhead. For an alternative pattern, check out the [Duck Pattern](#duck-pattern) in the Bonus section at the end of this README.

Finally, we create a `store.js` file where we'll import Redux and create our `store`.

### I Do: Create a new order

In a few minutes, we'll have full CRUD on our orders using Redux. Let's start by creating a new order.

**Step 1:**
Create a new constant to represent this action:

```js
// constants/order.js
export const CREATE_ORDER = 'CREATE_ORDER'
```

**Step 2:**
Create a new action creator.

```js
// actions/order.js
import { CREATE_ORDER } from "../constants/order";

export function createNewOrder(productName, quantity) {
  return {
    type: CREATE_ORDER,
    payload: {
      productName,
      quantity,
      status: 'pending'
    }
  }
}
```

Our action creator is just a JavaScript Function that returns a plain JavaScript Object. If we're creating an order for the new Taylor Swift album on vinyl and we want 3 copies, we would invoke this action creator like this:

```js
const order = createNewOrder('Reputation (Vinyl)', 3)
```

The object we would get back (to send to our reducers) would look like this:

```js
const order = {
	type: 'CREATE_ORDER',
	payload: {
		productName: 'Reputation (Vinyl)',
		quantity: 3,
		status: 'pending'
	}
}
```

Our actions are just plain JavaScript object. The only required property is the `type` property, which is how we'll know how to update state in our reducer. Otherwise, the object can take any shape. As you might imagine, not standardizing the shape can lead to confusion and errors, so here we're following a common standard for actions called the [Flux Standard Action](https://github.com/acdlite/flux-standard-action).

**Step 3:**
We're almost ready to update our state using Redux; we'll next build out our reducer:

```js
import { CREATE_ORDER } from "../constants/order";

const DEFAULT_STATE = {
  orders: []
}

export default function orderReducer(state = DEFAULT_STATE, action) {
  switch (action.type) {
    case CREATE_ORDER:
      return { 
	      ...state,
	      orders: [...state.orders, action.payload]
		  }
    default:
      return state;
  }
}
```

It's a good idea to provide your reducer with some default state, as we are here. This will help you to think about the structure of your state as well as ensure you're always returning some useful state from your reducer.

Your reducer needs to check the `type` property of the `action` object and then perform some update to state based on the value of `type`. You can do this with a simple `if`/`else if` statement, but the [`switch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch) statement is generally considered more readable and scalable.

**Step 4:**
The final step before we have our state managed by Redux is to create our `store` (where all our state will be `store`d).

```js
// store.js
import { createStore } from 'redux'
import orderReducer from './reducers/order'

export default createStore(orderReducer)
```

That's it! (For now.) We import the `createStore` function from redux and our `orderReducer` then invoke the `createStore` function, passing in our reducer function. There is a way to pass in more than one reducer, which you can read about in the [Using Multiple Reducers](#using-multiple-reducers) section of the Bonus section below.

**Step 5:**
Our store is all set up and ready to manage state for our application. For now, we'll just `console.log` state updates from `index.js`; in the near future, we'll integrate this with some React components.

```js
// index.js
import store from './store'
import { createNewOrder } from './actions/order'

console.log(store.getState())
store.dispatch(createNewOrder('Reputation', 1))
console.log(store.getState())
store.dispatch(createNewOrder('Red', 1))
console.log(store.getState())
```

In `index.js`, we need to import our `store` as well as the `action` we would like to dispatch. The `store` object has a couple of methods you'll use often, including `getState()`, `dispatch()` and `subscribe()`.

In the code snippet above, we're first logging the initial state to the console. We're then dispatching a new order action using the `dispatch` function and passing in the action object returned by our `createNewOrder` action creator. `dispatch()` takes an `action` and passes it to our reducer along with the current state.

We can refactor the snippet above to use the `subscribe()` method, which will invoke a callback function every time an action is dispatched to the store:

```js
// index.js
import store from './store'
import { createNewOrder } from './actions/order'

console.log(store.getState())
store.subscribe(() => console.log(store.getState()))

store.dispatch(createNewOrder('Reputation', 1))
store.dispatch(createNewOrder('Red', 1))
```

The callback we're passing in to `subscribe()` is getting the current state and logging it.

The processes we followed for creating our new order is a good workflow to follow generally when working with Redux. Start by defining a new constant, then create your new action creator, then define or update your reducer and you're ready to dispatch changes to the store.

### We Do: Remove an order

Now we'll work together to walk through removing an order from the store.

**Step 1:**
Define a new constant in `constants/order.js`:

```js
export const REMOVE_ORDER = 'REMOVE_ORDER'
```

**Step 2:**
Define a new action creator in `actions/order.js`:

```js
export const removeOrder = id => ({type: REMOVE_ORDER, payload: id})
```

**Step 3:**
Add a `case` statement to your reducer for removing an order:

```js
export default function orderReducer(state = DEFAULT_STATE, action) {
  switch (action.type) {
    case CREATE_ORDER:
      return { ...state, orders: [...state.orders, action.payload] };
    case REMOVE_ORDER:
      return {
	      ...state,
	      orders: state.orders.filter((order, id) => {
	        // the id of the order we want to remove is
	        // stored at action.payload
          return id !== action.payload;
        })
	    };
    default:
      return state;
  }
}
```

**Step 4:**
Now we can use our new action in `index.js`:

```js
store.dispatch(removeOrder(0))
```

### You Do: Update an order

Build out the functionality for updating an existing order. Given an id for an order and an object with the property/properties to update, return a new list of orders with the updated order.

<details> 
<summary>Solution</summary>

**Step 1:**
```js
// constants/order.js
export const UPDATE_ORDER = 'UPDATE_ORDER'
```

**Step 2:***
```js
// actions/order.js
export const updateOrder = (id, updatedOrder) => ({
  type: UPDATE_ORDER,
  payload: {
    id,
    updatedOrder
  }
})
```

**Step 3:**
```js
// reducers/order.js
export default function orderReducer(state = DEFAULT_STATE, action) {
  switch (action.type) {
    case CREATE_ORDER:
      return { ...state, orders: [...state.orders, action.payload] };
    case REMOVE_ORDER:
      return { ...state, orders: state.orders.filter((order, id) => {
          return id !== action.payload;
        }) };
    case UPDATE_ORDER:
      return { ...state, orders: state.orders.map((order, index) => {
        if (index !== action.payload.id) return order // we don't care about this one

        return { ...order, ...action.payload.updatedOrder };
      }) };
    default:
      return state;
  }
}
```

**Step 4:**
```js
// index.js
store.dispatch(updateOrder(0, { quantity: 20}))
store.dispatch(updateOrder(0, { status: 'shipped'}))
```

</details>

## Implementing Redux with React
- build out UI components for our inventory management system
- take our working store and integrate it with our new UI components
- add a form for adding items to the warehouse
  - discuss 2 kinds of state: app state and UI state
  - not all state needs to be stored in Redux or in components - can use both
  - modal for form = UI state, control that in a component; inventory = app state, control that in Redux
- revisit container v presentational components

## We Do: Shopping Cart in Redux (45 min)

[Building a Shopping Cart in Redux](https://git.generalassemb.ly/ga-wdi-exercises/react-redux-shopping-cart)

## Bonus

## Further Reading

We've just scratched the surface of what you can accomplish with Redux. Review the Redux documentation for more:

- [Updating state without mutating it (immutability)](https://redux.js.org/docs/recipes/reducers/ImmutableUpdatePatterns.html)
- [Using Redux with React Router](https://redux.js.org/docs/advanced/UsageWithReactRouter.html)
- [Working with asynchronous actions](https://redux.js.org/docs/advanced/AsyncActions.html)
- [Reducing boilerplate](https://redux.js.org/docs/recipes/ReducingBoilerplate.html)
- [Structuring reducers](https://redux.js.org/docs/recipes/StructuringReducers.html)

## Using Multiple Reducers

> `combineReducers()`

## Duck Pattern
The [ducks](https://github.com/erikras/ducks-modular-redux) pattern
- lots of files for related actions
- `ducks` from the last syllabul of redux
- organize code into "modules" with types, actions and reducers all in one file

> build out warehouse solution branch using duck pattern




