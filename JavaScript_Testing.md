# Writing test for JavaScript project using Jest

## These are the steps we need to follow to setup Jest.

- https://jestjs.io/docs/getting-started
- Install Jest

```bash
npm install --save-dev jest
```

- Create a .gitignore file and add node_modules to it.
- If package.json did't get created automatically run npm init.
- We need to install babel so we can run our code in node. (using import something from "./" is something for the browser and node wouldn't understand it. babel will transpile our code so node understands these imports)

```bash
npm install --save-dev babel-jest @babel/core @babel/preset-env
```

- configure Babel to target current version of Node by creating a babel.config.js file in the root of the project.

```js
module.exports = {
  presets: [["@babel/preset-env", { targets: { node: "current" } }]],
};
```

- Add a script to package.json

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

- Create a dummy test and call it test.js

```js
test("testing the setup", () => {
  expect(true).toBe(true);
});
```
