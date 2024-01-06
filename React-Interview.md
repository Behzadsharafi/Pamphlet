## What is JSX?

- It stands for JavaScript and XML. It provides and XML like syntax for us to write the javascript and render things out to the DOM.
- It's essentially a syntactic sugar over React.createElement. (Ultimately your react code is compiled down into that, so at each point react is simply creating elements under the hood even though it looks like we are writing HTML)

## What is class className in React?

- class is a reserved keyword in JavaScript. The jsx is ultimately getting compiled up to JavaScript so there will be a conflict if you simply use class to do your css styling. so JavaScript would think you are trying to create a JavaScript class and not "Style an Element". Under the hood babel is doing the transpilation of returning it to JavaScript.

## Describe data flow in React. (Do more research)

- The data flow is unidirectional. Meaning all of the components in react have a parent-child relationship and typically what is happening is you are passing data down from above going in one direction through props. Using global state management and context (read about redux) you share data multiple layers deep and you can avoid prop drilling. Data is managed in react locally in component state and all the way up to global app state.

## How would you delay an api call until a component is mounted? (Do a research on how to replicate the other lifecycle methods using useEffect)

- The traditional way in a class would be to use componentDidMount which essentially is run after the component has mounted (read more about lifecycle methods). So we make sure we are not making the api call before the component has mounted and kind of missing the appropriate stage in the lifecycle to render the data out.
- Now that we have functional components and hooks and we don't need to write classes anymore we don't need componentDidMount so the way that I would do this is to use the useEffect hook and pass an empty array as the dependency and what it does is it essentially mimics the componentDidMount and so then inside the useEffect we would write the api call and it would work basically the same way.

## Should you use ternaries or && operator to conditionally render React components?

- We should use ternaries because if you use && operators you end up encountering this certain kind of problem where for example you are checking for the length of an array. If you use the && operator, if the length is zero javascript won't evaluate the right side and instead of not seeing anything we will see a zero on the page.
- Another example is when we use an error on the left hand side of && operator. If the error is undefined we will have an uncaught error in the console because it evaluates to undefined.
  -Still we could solve it using !!contacts.length && ... or contacts.length > 0 && ... or even Boolean(contacts.length) && ..., but I still prefer not abusing the logical AND operator for rendering. I prefer being explicit by using a ternary.

## What is DOM?

- Represents the content of xml or html document as tree structure.
- Can easily read,access, and update the content of the document.
- Is a programming interface (API) and can be manipulated using a scripting language like javascript.

## What is Virtual DOM?

It is a copy of the actual DOM. When we make a change react makes the change to the virtual dom and the compares it to the actual DOM. Then it updates only that part of the DOM that is updated.
