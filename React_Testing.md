### End to End

- Cypress
- Playwright
- Selenium

### Component Testing

- React Testing Library

### What do we test?

- Does it render?
- Does it render the correct things?
- Does it do what it's supposed to?
- Does it respond correctly to user actions?

### What are the steps?

- npm install vitest --save-dev
- npm install jsdom --save-dev
- npm install @testing-library/react --save-dev
- npm install @testing-library/jest-dom --save-dev
- npm install @testing-library/user-event --save-dev
- We can install all at one go
  - npm install vitest jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event --save-dev
- Add a testing script to Package.json
- We can use https://github.com/vitest-dev/vitest/tree/main/examples , and navigate to react-testing-lib

```jsx
    "scripts": {
        "dev": "vite",
        "build": "vite build",
        "preview": "vite preview",
        "test": "vitest"
    },
```

- update vite.config.js as below; (the references are necessary for typescript)

```js
/// <reference types="vitest" />
/// <reference types="vite/client" />

import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: "./src/config/setup-test.js",
  },
});
```

- If typescript got to tsconfig.json and in "compilerOptions" add

```json
"types": ["vitest/globals"]
```

- Add the setup.js file to src/config/setup.js

```js
import { afterEach } from "vitest";
import { cleanup } from "@testing-library/react";
import "@testing-library/jest-dom/vitest";
import "@testing-library/jest-dom"; //for typescript I had to add this line

// clean up after every test - unmount my components
afterEach(() => {
  cleanup();
});
```

- If there was a problem for toBeVisible() use the exact same setup as per the vitest library.

```ts
/// <reference types="vitest" />
/// <reference types="vite/client" />

import react from "@vitejs/plugin-react";
import { defineConfig } from "vite";

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: "./src/test/setup.ts",
    // you might want to disable it, if you don't have tests that rely on CSS
    // since parsing CSS is slow
    css: true,
  },
});
```

- This is the only setup used.

```ts
import "@testing-library/jest-dom";
```

- Add a dummy test.

```jsx
describe("Setup test", () => {
  it("checks true", () => {
    expect(true).toBe(true);
  });
});
```

- DevDependencies in the package.json should look like this;

```json
  "devDependencies": {
    "@testing-library/jest-dom": "^6.1.6",
    "@testing-library/react": "^14.1.2",
    "@testing-library/user-event": "^14.5.2",
    "@types/react": "^18.2.15",
    "@types/react-dom": "^18.2.7",
    "@vitejs/plugin-react": "^4.0.3",
    "eslint": "^8.45.0",
    "eslint-plugin-react": "^7.32.2",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.3",
    "jsdom": "^23.0.1",
    "sass": "^1.68.0",
    "vite": "^4.4.5",
    "vitest": "^1.1.0"
  }
```

- We can run the test by typing npm run test.
- In vitest all tests need to be jsx
