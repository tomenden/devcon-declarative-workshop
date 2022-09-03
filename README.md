# Wix Devcon - Declarative Frontend

Welcome to the Wix Declarative Frontend workshop.  
Today we will explore some alternatives to `$w` by adding some functionality to a Todo App using declarative libraries. Let's get started!

## What you will build

You will add filtering and count functionality to an existing TodoApp in 3 different variations:
* The imperative way
* Using `react-velo`
* Using `velo-mvvm`

### Setup
1. Open [this](https://editor.wix.com/html/editor/web/renderer/new?siteId=c56e81d3-c41e-47dc-ac02-9eef427733dc&metaSiteId=2caacb23-d639-4490-a2e7-e7b2dc2f5a22&autoDevMode=true) template (make sure you are logged into Wix)
2. **Preview** and get a feel for how the app works. Add some Todos, mark them as completed, delete some Todos, play around.
3. Select different Filters on the left - currently, changing the filter does nothing. Observe the "Items Left: 0" counter. Currently, it does not update when you change the state of your Todo items. Let's change that and make it work!
4. Exit Preview, turn on Developer Mode and take a look at the code.


### Site structure
The site has 3 pages: `imperative`, `react-velo`, `velo-mvvm`.  
In each page's code there are *TODO* comments instructing you to add the missing functionality. Feel free to complete the tasks in whatever order you'd like.

### todo-helpers
If you look at the `public` folder you will see a `todo-helpers.js` file there.  
It exposes some useful functions to create, filter and sort Todos.  
The functions are already imported in all of the pages and you can use them.  
If you want to add some additional utility functions, you are welcome to do so, but note that no additional function is required to complete the tasks of this workshop.

### Table of Contents
  * [Imperative ($w)](##imperative-w)
    + [Task 1: Filter Radio Buttons](#task-1-filter-radio-buttons)
    + [Task 2: The "Items Left" Counter](#task-2-the-items-left-counter)
  * [react-velo](#react-velo)
    + [Task 1: Filter Radio Buttons](#task-1-filter-radio-buttons-1)
    + [Task 2: The "Items Left" Counter](#task-2-the-items-left-counter-1)
  * [velo-mvvm](#velo-mvvm)
    + [Task 1: Filter Radio Buttons](#task-1-filter-radio-buttons-2)
    + [Task 2: The "Items Left" Counter](#task-2-the-items-left-counter-2)
  * [Appendix](#appendix)
    + [`React`](#react)
    + [`mobx` & Reactive Programming](#mobx--reactive-programming)

## Imperative ($w)
Imperative programming is all about the **How**.  
When something in our program happens (e.g. a user interaction, a state change) we need to instruct the program exactly what needs to happen as a result.  
Let's start by implementing the filter functionality. 

### Task 1: Filter Radio Buttons
The nickname of our Filter Radio Group is `#filterGroup`.  
The values it can have are `"all" | "completed" | "notCompleted"`. You can click on it in the editor and then click on **Manage Choices** in the floating panel to see it for yourself.

A Radio Group has an `onChange` function that takes a callback function that will be triggered any time the value changes. You can read about it in the docs [here](https://www.wix.com/velo/reference/$w/radiobuttongroup/onchange).

Implement the filtering functionality.


<details>
  <summary>Hint</summary>

  In imperative programming, we want to think about how changes in state/UI effect other pieces of our program.  
  What needs to change when the `filterGroup` value changes?  

  The `#todoList` repeater.
  
</details>

<details>
  <summary>Solution</summary>

  ```js
  filterGroup.onChange((e) => {
    filter = e.target.value
    // updateList is a helper function located at the bottom of this file.
    // Take a look at its implementation
    // It updates the list by the selected filter.
    updateList()
    })
  ```
</details>

### Task 2: The "Items Left" Counter
`#notCompletedCount` is a Text element that shows the number of items that are not completed. Implement its behavior.


<details>
  <summary>Hint</summary>

  This counter should update in the following scenarios:
  1. When a new item is added.
  2. When an existing item is marked as completed.
  3. When an existing item is deleted.
   
  Since this update needs to happen in multiple places, start by writing an `updateNotCompletedCount()` function that will update the element's `text` to the correct value.  
  Then, call this function in all of the scenarios listed above.
  
</details>

<details>
  <summary>Solution</summary>

  ```js

  todoInput.onKeyPress(e => {
    //... existing code, and then
    updateNotCompletedCount()
  })

  doneCheckbox.onChange(() => {
    //... existing code, and then
    updateNotCompletedCount()
  })

  xButton.onClick(() => {
    //... existing code, and then
    updateNotCompletedCount()
  })


  function updateNotCompletedCount() {
    notCompletedCount.text = `Items Left: ${
      getFilteredTodos(todos, 'notCompleted').length
    }`
  }
  ```
</details>


## react-velo
`react-velo` brings [React](https://reactjs.org/) to Velo!  
You build your UI using the drag-and-drop editor, describe your UI with JSX (your elements are available under the `W` namespace) and write React code!

You can read more about `react-velo` in the official README [here](https://www.npmjs.com/package/@wix/react-velo).
More resources on React can be found at the Appendix section of this document.

### Task 1: Filter Radio Buttons
The filter group is available as a React Component under `W.filterGroup`. Add the desired behavior to the filter group so that the list is updated any time the filter selection changes.

<details>
  <summary>Hint</summary>

  Declarative programming is all about the **what**.
  When changing the value of the filterGroup, what needs to change?

  The value of the **filter state**.  
  Note that this piece of state is already added for you (line 11) and that it is initially set to `all`.  
  By leveraging react-hooks we also have a setFilter function that will update this piece of state. Any piece of your component that cares about this state will update **automatically** - so you only care about **what** needs to happen, and not how it actually happens.  

  If you are new to React that's perfectly fine, you will find some great resources in the appendix section to learn more. To get you started, this is how you would write this component using jsx, passing an `onChange` Prop (syntactically, Props are just like attributes of an HTML element)

  ```jsx
    <W.filterGroup onChange={(e) => {
        //add your function implementation here
    }} />
  ```
  
</details>

<details>
  <summary>Solution</summary>

  ```jsx

  <W.filterGroup
      onChange={(e) => {
        setFilter(e.target.value)
      }}
    />
  ```
</details>

### Task 2: The "Items Left" Counter
The "notCompleted" text element is available to you as a React component under `W.notCompleted`.
Render this component in your app so its text reflects the correct number of Todo Items left to complete.

<details>
  <summary>Hint</summary>

  This example beautifully exemplifies the power of declarative programming. All you need to do is describe what `W.notCompleted` needs to show, you don't care about the various use-cases in which it needs to be updated.  

  If you are unfamiliar with React, this is what you need to render this component, passing it the `text` prop:

  ```jsx
    <W.notCompletedCount text={`write the desired value of the text here`} />
  ```
  
</details>

<details>
  <summary>Solution</summary>

  ```jsx

  <W.notCompletedCount
        text={`Items Left: ${getFilteredTodos(todos, 'notCompleted').length}`}
      />
  ```
</details>

## velo-mvvm
`velo-mvvm` is another library that embraces the Declarative Programming Paradigm, but does it differently than `react-velo`. It uses `mobx`, which is a "Reactive" library, to create an observable state that you can bind your view to. Your view-elements automatically react to changes in the observed state. You can find some great resources on `mobx` and Reactive Programming in the Appendix section of this document.

In `velo-mvvm`, you create the observable state by calling the `createModel` function. You then use `bindView` and the special repeaters function `bindRepeaters` to bind your view to the state.

You can read more about `velo-mvvm` in the official README [here](https://www.npmjs.com/package/@wix/velo-mvvm).

### Task 1: Filter Radio Buttons
Add the desired behavior to the filter group so that the list is updated any time the filter selection changes.
The element's nickname is `#filterGroup`.
Use the `bindView` function from `velo-mvvm`.

<details>
  <summary>Hint</summary>

  `bindView` accepts a javascript object. Each property is a nickname, and the value is always a function.  
  In reactive-programming, functions are first-class citizens. Whenever a relevant piece of the state changes, the bound function will re-run and the element will update.

  Let's see where you would add the code for the `#filterGroup` RadioGroup:
  ```js
    bindView({
        //... the rest of the bindings that already exist
        '#filterGroup': {
            onChange: (e) => {
                // your code here
            },
        }
    })
  ```

  * Note: You can also call `bindView` multiple times, so feel free to either add it as an additional property to the existing call or call it separately any place that you'd like.
  
</details>

<details>
  <summary>Solution</summary>

  ```js
  bindView({
    '#filterGroup': {
      onChange: (e) => {
        model.filter = e.target.value
      }
    }
  })
  ```

  Note how just like in the `react-velo` example, this piece of code does not care what needs to happen in reaction to the state change or how this state transition occurs. It only cares about updating the piece of state it is in charge of, and everything else just works automatically!
</details>


### Task 2: The "Items Left" Counter
The nickname of the "notCompleted" text element is `#notCompletedCount`. Implements the code so that it correctly shows the number of items left.

<details>
  <summary>Hint</summary>

  The `#notCompletedCount` element should reflect the number of todos that are not completed. It does not care **how** and when a new uncompleted item arrives or how an item is transitioned between the completed and uncompleted state. It just needs to perform a calculation, and whenever the relevant pieces of state update, its value will also automatically update!

  Once again, declarative programming shines :) 

  You want to bind the `text` value of this element to something, and you can do it by using this piece of code
  
  ```js
    bindView({
        '#notCompletedCount': {
            text: () => `write the text you want to be shown here`
        }
    })
  ```

  Note: you have a useful `getFilteredTodos` function that you can use to get the `notCompleted` todos easily.
  
  
</details>

<details>
  <summary>Solution</summary>

  ```js
    bindView({
        '#notCompletedCount': {
            text: () =>
                `Items Left: ${getFilteredTodos(model.todos, 'notCompleted').length}`,
        }
    })

  ```
</details>


## Appendix
* [`react-velo` docs](https://www.npmjs.com/package/@wix/react-velo)
* [`velo-mvvm` docs](https://www.npmjs.com/package/@wix/velo-mvvm)

### `React`
* [React's official site](https://reactjs.org/)
* [React's docs](https://reactjs.org/docs/getting-started.html) (Highly recommended)
* [Thinking in React](https://reactjs.org/docs/thinking-in-react.html)
* [A beginner's guide to React by Kent C. Dodds](https://egghead.io/lessons/react-a-beginners-guide-to-react-introduction) (egghead.io)

### `mobx` & Reactive Programming
* [`mobx`'s official README](https://mobx.js.org/README.html)
* [egghead.io videos on mobx](https://egghead.io/q/mobx)
* [Introduction to Reactive Programming using MobX](https://medium.com/machine-words/introduction-to-reactive-programming-using-mobx-2c032cac818e)
* [Becoming fully reactive: an in-depth explanation of MobX](https://hackernoon.com/becoming-fully-reactive-an-in-depth-explanation-of-mobservable-55995262a254)