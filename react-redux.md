# Part 2: React and Redux

## Learning Objectives

- Explain how Redux is useful in a React app for managing state
- Explain how Redux fits into a React app
- Setup a React app to use Redux
- Dispatch state changes from within React components

## Framing (5 min / 0:05)

We previously covered Redux at a high level, how it works and why it's useful
for building applications with React, particularly when we are managing a lot of
state.

We're going to review Redux, build out an example using more complex state, then
integrate our working state management with some prebuilt React components.

## Review: Redux (10 min / 0:15)

What are the three parts of Redux? How does each part work?

<details>

  <summary>**3 Parts of Redux**</summary>

1. Store
2. Reducers
3. Actions

</details>

But there's more to Redux than the three parts! How does Redux work? Well, it works a lot like Amazon does.

There's this massive warehouse with all this stuff in it. This
warehouse is way to large for poor ol' Jeff Bezos to run by himself so, being
the eccentric billionaire that he is, he's built a horde or Robot Minions to manage it for him.

Each Robot Minion is in charge of some part of the warehouse. Whenever someone
needs something from the warehouse an Invoice is sent to the warehouse,
to the appropriate Robot Minion, who then carries out the order.

### Think Pair Share: Which is Which?

We just discussed how there are three parts to Redux:

1. Store
2. Reducers
3. Actions

And how Redux is similar to Amazon:

1. Warehouse
2. Robot Minions
3. Invoices

But which is which? Which part of Amazon is the Reducer, the Store or the Actions?

Turn and talk with your neighbor or the people in your row.

<details>

  <summary>**Solution**</summary>

So Redux doesn't really work the same way Amazon does, but kind of. All our
state is stored in a single place (the warehouse). Whenever we want to update
our state, we do so with a reducer (Robot Minion). The reducer needs to know
what operation you're asking it to perform on the current state and any new data
that should become part of state, which it does through a object literal called
an action (Invoice).

</details>

## Redux (5 min / 0:20)

We are going to build out a simple warehouse inventory management tool using
Redux and React over the course of this lesson. We will start by building out
the Redux functionality for review and then integrating it with our React
components.

### We Do: React Redux Warehouse

Clone down [this
repository](https://git.generalassemb.ly/ga-wdi-exercises/react-redux) for the
exercise and run `npm install`.

```sh
git clone git@git.generalassemb.ly:ga-wdi-exercises/react-redux.git
cd react-redux
npm install
```

### You Do: Review the Redux solution branch (10 min / 0:30)

Checkout the `solution-redux` branch. Explore the codebase and review the
following questions:

- Where are the actions?
- Where is the store?
- Where are the reducers?
- How is the reducer structured?

When you're finished, checkout the `master` branch.

### We Do: Project setup (5 min / 0:35)

First we need to build out the project structure:

```sh
cd src/
mkdir actions constants reducers
touch actions/order.js constants/order.js reducers/order.js store.js
cd ..
```

We're creating three directories:

  1. `actions/`
  2. `constants/`
  3. `reducers/`

The `actions/` directory is where we'll build out our action creators - functions
that return an object literal representing a redux action. The `reducers/`
directory is where we'll build out our reducers - functions that take our
current state and an action and returns a new, updated state object. The
`constants/` directory is where we'll define constant variables representing our
action types.

We're grouping related functionality by filename: `order.js`. Splitting
functionality in this way is common but comes with a lot of overhead. For an
alternative pattern, check out the [Ducks Pattern](#duck-pattern) in the Bonus
section at the end of this README.

Finally, we create a `store.js` file where we'll import Redux and create our
`store`.

### I Do: Create a new order (20 min / 0:55)

In a few minutes, we'll have full CRUD on our orders using Redux. Let's start by
creating a new order.

**Step 1:** Create a new constant to represent this action:

```js
// constants/order.js
export const CREATE_ORDER = 'CREATE_ORDER'
```

**Step 2:** Create a new action creator.

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

Our action creator is just a JavaScript Function that returns a plain JavaScript
Object. If we're creating an order for the new Taylor Swift album on vinyl and
we want 3 copies, we would invoke this action creator like this:

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

Our actions are just plain JavaScript object. The only required property is the
`type` property, which is how we'll know how to update state in our reducer.
Otherwise, the object can take any shape. As you might imagine, not
standardizing the shape can lead to confusion and errors, so here we're
following a common standard for actions called the [Flux Standard
Action](https://github.com/acdlite/flux-standard-action).

**Step 3:** We're almost ready to update our state using Redux; we'll next build
out our reducer:

```js
// reducers/order.js
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
      };

    default:
      return state;
  }
}
```

It's a good idea to provide your reducer with some default state, as we are
here. This will help you to think about the structure of your state as well as
ensure you're always returning some useful state from your reducer.

Your reducer needs to check the `type` property of the `action` object and then
perform some update to state based on the value of `type`. You can do this with
a simple `if`/`else if` statement, but the
[`switch`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch)
statement is generally considered more readable and scalable.

**Step 4:** The final step before we have our state managed by Redux is to
create our `store` (where all our state will be `store`d).

```js
// store.js
import { createStore } from 'redux'
import orderReducer from './reducers/order'

export default createStore(orderReducer)
```

That's it! (For now.) We import the `createStore` function from redux and our
`orderReducer` then invoke the `createStore` function, passing in our reducer
function. There is a way to pass in more than one reducer, which you can read
about in the [Further Reading](#further-reading) bonus section below.

**Step 5:** Our store is all set up and ready to manage state for our
application. For now, we'll just `console.log` state updates from `index.js`; in
the near future, we'll integrate this with some React components.

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

In `index.js`, we need to import our `store` as well as the `action` we would
like to dispatch. The `store` object has a couple of methods you'll use often,
including `getState()`, `dispatch()` and `subscribe()`.

In the code snippet above, we're first logging the initial state to the console.
We're then dispatching a new order action using the `dispatch` function and
passing in the action object returned by our `createNewOrder` action creator.
`dispatch()` takes an `action` and passes it to our reducer along with the
current state.

We can refactor the snippet above to use the `subscribe()` method, which will
invoke a callback function every time an action is dispatched to the store:

```js
// index.js
import store from './store'
import { createNewOrder } from './actions/order'

console.log(store.getState())
store.subscribe(() => console.log(store.getState()))

store.dispatch(createNewOrder('Reputation', 1))
store.dispatch(createNewOrder('Red', 1))
```

The callback we're passing in to `subscribe()` is getting the current state and
logging it.

#### Aside: The Process of Working with Redux

The processes we followed for creating our new order is a good workflow to
follow generally when working with Redux. Start by defining a new constant, then
create your new action creator, then define or update your reducer and you're
ready to dispatch changes to the store.

All together, that means there are three steps to follow:

1. Create a new constant in your `constants/` directory
2. Create a new action creator in `actions/`
3. Update your reducer

The first three steps you'll almost always follow, in order whenever you need to
update Redux.

> **HINT:** You will almost always want to follow these 3 steps when working
> with Redux

### We Do: Remove an order (10 min / 1:05)

Now we'll work together to walk through removing an order from the store.

**Step 1:** Define a new constant in `constants/order.js`:

```js
export const REMOVE_ORDER = 'REMOVE_ORDER'
```

**Step 2:** Define a new action creator in `actions/order.js`:

```js
export const removeOrder = id => ({type: REMOVE_ORDER, payload: id})
```

**Step 3:** Add a `case` statement to your reducer for removing an order:

```js
export default function orderReducer(state = DEFAULT_STATE, action) {
  switch (action.type) {
    case CREATE_ORDER:
      return {
        ...state,
        orders: [...state.orders, action.payload]
      };

    case REMOVE_ORDER:
      return {
        ...state,
        orders: state.orders.filter((order, id) => {
          return id !== action.payload;
        })
      };

    default:
      return state;
  }
}
```

**Step 4:** Now we can use our new action in `index.js`:

```js
store.dispatch(removeOrder(0))
```

### You Do: Update an order (10 min / 1:15)

Build out the functionality for updating an existing order. Given an id for an
order and an object with the property/properties to update, return a new list of
orders with the updated order.

<details> 
<summary>**Solution**</summary>

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
      return {
        ...state,
        orders: [...state.orders, action.payload]
      };

    case REMOVE_ORDER:
      return {
        ...state,
        orders: state.orders.filter((order, id) => {
          return id !== action.payload;
        })
      };

    case UPDATE_ORDER:
      return {
        ...state,
        orders: state.orders.map((order, index) => {
          if (index !== action.payload.id) return order;

          return {
            ...order,
            ...action.payload.updatedOrder
          };
        })
      };

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

## Break (10 min / 1:25)

## Implementing Redux with React (5 min / 1:30)

Now that we have Redux working and are managing and updating state inside a
Redux store, we're ready to connect our store to our React components. Once we
do, we'll be able to dispatch actions to the store from inside our React
components. When a user clicks a button or submits a form, we can use Redux to
update our state, all without having to pass a bunch of callbacks around!

### You Do: Review the React-Redux solution branch (10 min / 1:40)

Commit the work that you have so far on the `master` branch. Then checkout the
`solution-react-redux` branch. Explore the codebase and review the following
questions:

- Where are we requiring `react-redux`? (The npm package with the React bindings
  for Redux)
- Across the codebase, what are we using from `react-redux`?
- Are we using our actions anywhere inside our components?
- What might `Provider` be? What might it be doing?
- What could `connect()` be doing?
- What about `mapStateToProps()`?
- What about `mapDispatchToProps()`?

When you're finished, checkout the `master` branch again.

### Connecting Redux and React (10 min / 1:50)

We will need some way of accessing our Redux store from inside our React
components. The simplest way to do this could be to just pass it around through
props, but one of the key benefits of Redux is avoiding having to pass state as
props to deeply nested components.

Redux gives us a React component, called `<Provider />` that we can pass our
store to as a prop. The `Provider` component will then use use some
[magic](https://reactjs.org/docs/context.html) to make our store available to us
throughout our component tree.

Let's install `react-redux`:

```sh
npm install react-redux --save
```

We can import the `Provider` component from `react-redux` and then pass our
`App` component as a child to the `Provider` component:

```js
// ...
import { Provider } from "react-redux";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
```

We are now ready to use our Redux managed state from within our React
components.

### Creating New Orders (10 min / 2:00)

As we've been learning React, we've stressed the difference between
presentational and container components. That distinction is especially
important when working with Redux: we want to keep our containers and our view
components separate. If our application state is complex enough that it warrants
using Redux, then we likely have either a lot of state we're trying to manage,
state that is in a somewhat complex structure to manage or both. Therefore, the
distinction between components that affect state and those that just present it
is all the more important.

The `OrderForm` component is presentational; we need to build out the
corresponding container component using Redux, which we'll do in the
`NewOrderForm` component. We're going to build out this container component in
the `src/containers/` directory.

When we wanted to create a new order using just Redux, we did so with this line
of code:

```js
store.dispatch(createNewOrder('Reputation', 1))
```

We want to do something very similar, this time from within a React component.
We need a way of accessing the store's `dispatch()` method, which we can do with
the `connect()` method provided by `react-redux`.

`connect()` takes a React component and wraps it by returning a new component
class connected to Redux. `connect()` takes two arguments (that we will dive in
to later) and returns the wrapper for our component. For now, we just need get
our wrapper and then pass our component to the wrapper:

```js
import { connect } from 'react-redux'
import OrderForm from "../components/OrderForm";

const wrapperFunction = connect()
const NewOrderForm = wrapperFunction(OrderForm)

// the above is often shortened to:
// const NewOrderForm = connect()(OrderForm)

export default NewOrderForm
```

We need to go update our `App` component so that it's rendering our
`NewOrderForm` component. If we go and look at our React app in the browser, it
won't seem as though anything has changed. But calling the wrapper function
around our `OrderForm` component does something important: it passes
`dispatch()` in to our component as a prop. If we add the following line to the
`render()` function of our `OrderForm` component, we'll see `dispatch()`
included in the object that is printed to the console:

```js
console.log(props)

// If everything is working, you should see something like this in the console:
// {dispatch: f}
```

We now have `dispatch()` in the props of our component! That means we can now
use it to send actions to the store and update our state!

The form submission is handled by the `handleSubmit()` method. We need to import
the action creator for the action we want to dispatch (`createNewOrder()`).
Then, if we add the following line to the `handleSubmit()` method, we will be
dispatching our action to the store to update our application's state:

```js
this.props.dispatch(createNewOrder(productName, quantity));
```

### Passing State from Redux as Props (10 min / 2:10)

Now that we can update our state from within our components, it would be nice to
read that state and pass it to our presentational components. Outside of React,
we used the `getState()` method to read our state; however, the `connect()`
method abstracts this away for us.

We'll be working on the `OrderTable` component from now on. We want to pass in
our state so that we can render a table of all the outstanding orders. Each row
will represent a single order. Each row will also include a button for deleting
that order, an input for updating the quantity needed for that order, and a
dropdown for changing the order status.

The first argument you can pass in to the `connect()` method is typically called
`mapStateToProps`. It's a way of taking the entire state object and returning
some subset of it, specifically the part(s) of state that the presentational
component will display and nothing more. `mapStateToProps` is a callback that
must return an object. A simple version would look something like this:

```js
const mapStateToProps = state => ({
  orders: state.orders
})
```

Then if we call `connect()` with our `Orders` component, we can get something
like this:

```js
const OrderTable = connect(mapStateToProps)(Orders)
```

If we add a `console.log` to our `Orders` component, we'll see the `orders`
property containing each of the orders we've added to our store. We can then
output these into a table using the `<Table />` and `<TableRow />` components.

### Passing Dispatch Methods (10 min / 2:20)

We could at this point make our state updates using `dispatch()` like we did
with our form. But `connect()` takes a second argument: `mapDispatchToProps`. It
can be either an object or a function.

If it is an object, each property must be an action creator and `connect()` will
wrap each function into a call to `dispatch()`.

More often, you'll see `mapDispatchToProps` defined as a function that returns
an object. In that case, the `mapDispatchToProps` function receives `dispatch()`
as an argument and each property of the returned object should be a function
that calls `dispatch()`, passing in some object creator.

We can use `mapDispatchToProps` to add our remove button to each order table
row. If we import the `removeOrder` action creator, we can then create a simple
`mapDispatchToProps()` signature that looks like this:

```js
const mapDispatchToProps = dispatch => ({
  onRemove: id => dispatch(removeOrder(id))
})
```

Then we update our `connect()` call to look like this:

```js
const OrderTable = connect(mapStateToProps, mapDispatchToProps)(Orders);
```

Now we'll have an `onRemove()` method passed into our `Orders` component as a
prop and we can use this in the `onClick` handler for our button to remove an
order.

## You Do: Update an Order (10 min / 2:30)

Add a method to your `mapDispatchToProps()` that takes an id for an order and an
update objected dispatches an action using the `updateOrder` action creator.

## Closing

We've covered a lot here so be sure to go back and review, ensuring your can
explain what each part is doing. Believe it or not, what we've covered here
really only scratches the surface of what you can do with Redux. For more,
review the [Redux documentation](https://redux.js.org/docs), specifically the
sections listed in the [Further Reading](#further-reading) section below.

## Bonus

### You Do: Cheatsheet

One of the downsides to Redux is it has a lot of boilerplate: you have to write
pretty repetitive code to perform similar actions. The upside to that is, as a
learner, there are clear steps you follow to perform one of the CRUD actions in
Redux. That means, making yourself a cheat sheet for Redux is a really great
idea!

Define and explain the following:

* Action
* Reducer
* Store
* Constants
* What is an action creator? What does it do?
* What does `dispatch()` do?
* What does `createStore()` do?
* What are the 3 steps you follw to add some CRUD functionality to your store?
* What does `react-redux` do?
* What does `Provider` do?
* What's a container, in the context of Redux? How is a container component
  different from a presentational component?
* What does `connect()` do?
* What does `mapStateToProps` do?
* What does `mapDispatchToProps` do?

On the one hand, Redux can seem complicated and foreign and tricky to grasp. On
the other hand, Redux has a small footprint, so there isn't much to learn. It's
also (for better or for worse) very repetitive. You'll follow pretty much the
same 3 steps to perform some update on your state: create a constant, build out
an action creator, then update your reducer. You'll also follow a pretty similar
pattern when connecting container components to state and your store (so it can
dispatch actions): import the component, pass it to the wrapper from
`connect()`, pass in a `mapStateToProps` function and a `mapDispatchToProps`
function and voila.

### You Do: Shopping Cart in Redux

If you're looking for some extra practice, try building out the following
[Shopping
Cart](https://git.generalassemb.ly/ga-wdi-exercises/react-redux-shopping-cart)
exercise.

### Further Reading

We've just scratched the surface of what you can accomplish with Redux. Review
the Redux documentation for more:

- [Updating state without mutating it
  (immutability)](https://redux.js.org/docs/recipes/reducers/ImmutableUpdatePatterns.html)
- [Using Redux with React
  Router](https://redux.js.org/docs/advanced/UsageWithReactRouter.html)
- [Working with asynchronous
  actions](https://redux.js.org/docs/advanced/AsyncActions.html)
- [Using
  `combineReducers`](https://redux.js.org/docs/recipes/reducers/UsingCombineReducers.html)
- [Reducing
  boilerplate](https://redux.js.org/docs/recipes/ReducingBoilerplate.html)
- [Structuring
  reducers](https://redux.js.org/docs/recipes/StructuringReducers.html)

### Ducks Pattern

The way we've organized our store, constants, reducers and actions is very
common among applications that use Redux. But it has one major downside: if we
want to add or modify a feature, we need to do so across at least 3 different
files. If our application is big (or even if it isn't that big), this could
become tedious.

The [ducks](https://github.com/erikras/ducks-modular-redux) pattern is a way of
organizing your reducers, actions and constants by module.
