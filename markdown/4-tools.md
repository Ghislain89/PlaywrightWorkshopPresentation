### Tooling

- Setup & Configuration
- Parallelization
- Reports
- Code Generator
- Debugger
- Trace Viewer
- UI mode

----

### Setup & Configuration
* Initial set up as easy as running: ```npm init playwright@latest```
* Configuration exposed in through a ```TestConfig```
    * Parallelization
    * Browsers
    * Reporters
    * Global Timeouts
* We prefer folder structures that resemble the system under test structure.

----
### Parallelization

Playwright runs _files_ in parallel. Running _tests_ in parallel requires configuration

```ts 
import { test } from '@playwright/test';

test.describe.configure({ mode: 'parallel' });

test('runs in parallel 1', async ({ page }) => { /* ... */ });
test('runs in parallel 2', async ({ page }) => { /* ... */ });
```
```ts 
import { defineConfig } from '@playwright/test';

export default defineConfig({
  fullyParallel: true,
});
```

Note:
 that parallel tests are executed in separate worker processes and cannot share any state or global variables. Each test executes all relevant hooks just for itself, including beforeAll and afterAll.

----

### Reports - Configuring them
* You can either set the reporter through the CLI: ```npx playwright test --reporter=line```
* Or through the `playwright.config.ts`:
```ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [
    ['list'],
    ['json', {  outputFile: 'test-results.json' }]
  ],
});
```
* You can provide 1 or more reporters.

----

### Reports - Overview

----

### List
```shell
npx playwright test --reporter=list

Running 124 tests using 6 workers

 1  ✓ should access error in env (438ms)
 2  ✓ handle long test names (515ms)
```

----

### Line
```shell
npx playwright test --reporter=line

Running 124 tests using 6 workers
  1) dot-reporter.spec.ts:20:1 › render expected ===================================================

    Error: expect(received).toBe(expected) // Object.is equality

    Expected: 1
    Received: 0

[23/124] gitignore.spec.ts - should respect nested .gitignore
```

----
### Dot
```shell
npx playwright test --reporter=dot
Running 124 tests using 6 workers
······F·············································
```

----
### HTML reporter
* Contains Test & Step Information
* If enabled, trace files per testcase.

----
### Blob Reporter

* Outputs report in a blob
* Primairily used for merging multiple blobs to create a report afterwards.

----
### Community Plugins

* Playwright HTML Reporter: https://rodrigoodhin.gitlab.io/playwright-html/#/1.1.5/screenshots

----
### Code Generator
* Two ways of generating testcases
* Codegen will try to implement Playwright's best practices when generating locators

----
### Codegen with Vscode extension
![Alt text](../images/codegen.png)

----
### Codegen through CLI

```shell
npx playwright codegen demo.playwright.dev/todomvc
```


