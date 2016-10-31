# Redux Combine Reducers Lab

## Objectives

1. Build Redux's `combineReducers` function
2. Write two reducers to modify different pieces of application state
3. Use the `combineReducers` function to

## Instructions

In this lab, we'll see how Redux's combine reducers function lets us delegate different pieces of state to separate reducer functions.

We'll do this in the context of a book application that we'll use to keep track of programming books that we've read.

We want our app to do two things:
1. Keep track of all the books we've read: title, author, description, and our rating (number of stars).
2. Keep track of which books we'd want to recommend


The actions we need to implement are:

+ Adding a new book to the library
+ Adding a book to the favorites list

### Step 1: Determine Application State Structure

Our app will need a state object that stores two types of information:

1. All our books, in an array
2. The favorite books, also in an array

Whenever we add a book to our library, we need to dispatch an action that will cause it to be added to our array of books. Whenever we want to "favorite" a book, we want that book to be added to our list of recommended books.

We'll start by determining what our application state object will look like:

```
{
  books: // array of books,
  favoriteBooks: //array of favorite books
}
```

Because we have two top-level keys in our state object, we'll need two reducers. For simplicity, let's gives these functions the same name as our keys. This means the object that we'll pass into our `combineReducers` function will look like this:

```javascript
{
  books,
  recommendedBooks
}
```


### Step 2: Define Actions

Once we have our state structure defined, we can write our action creators. These functions return actions that specify how our state object will change.

These are the actions that we need to account for:
+ Adding a book to our key that holds all books
+ Removing a book from our list of all books
+ Adding a book to our list of recommended books
+ Removing a book from our list of recommended books

```javascript
function addBook(book){
  return {
    type: "ADD_BOOK",
    payload: book
  }
}

function addRecommendedBook(book){
  return {
    type: "ADD_RECOMMENDED_BOOK",
    payload: book
  }
}

function removeBook(book){
  return {
    type: "REMOVE_BOOK",
    payload: book
  }
}

function removeRecommendedBook(book){
  return {
    type: "REMOVE_RECOMMENDED_BOOK",
    payload: book
  }
}

```

Notice that each action has a type, represented by a string. If we wanted to, we could have saved these type strings as variables and imported them into the file where we have our action creators. This is a common pattern that you'll see, but it's not required.

### Step 3: Write Reducers

Next, we'll create our reducer functions. These functions will respond to the action types defined above. Create a reducers.js file inside our `/src` directory that holds these functions. (We'll keep our reducers in a single file but they don't need to be organized this way--they could be in their own individual files or organized in groups, whatever makes sense in the project!) Export the reducers so that we can use them inside index.js:


```javascript
export function books(state = [], action){
  switch (action.type) {
  case "ADD_BOOK":
    return [].concat(state, action.payload)
  case "REMOVE_BOOK":
    let idx = state.indexOf(book)
    return [].concat(state.slice(0, idx), state.slice(idx + 1, state.length))
  default:
    return state
  }
}


export function recommendedBooks(state = [], action){
  switch (action.type) {
  case "ADD_RECOMMENDED_BOOK":
    return [].concat(state, action.payload)
  case "REMOVE_RECOMMENDED_BOOK":
    let idx = state.indexOf(book)
    return [].concat(state.slice(0, idx), state.slice(idx + 1, state.length))
  default:
    return state
  }
}
```

Notice that we've provided a default value of an empty array for state in both functions because we expect both values to be arrays. We've also accounted for the default case. If our reducers receive an action other than adding or removing a book, they will return the original state object. Also note that in both reducers, we're creating a new state array and merging the values from the previous state with the action payload, rather than modifying the existing state object.


### Step 4: Combine Reducers

Now, let's dive into creating our combineReducers function. But first, let's think about what functionality we need this function to have.

First, we need `combineReducers` to return a **another** reducer function that we can pass into our store. We need this reducer to know how to delegate its reducer responsibilities for each piece of the state tree to the separate reducers stored on the object passed into `combineReducers`.
