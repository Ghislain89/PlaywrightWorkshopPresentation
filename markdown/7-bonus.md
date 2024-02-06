# Bonus

- Visual Regression Testing
- Accessability Testing
- Component Testing [out of scope for today]
- CI Integration
- Linting

----
### Visual Regression Testing
```ts []
import { test, expect } from '@playwright/test';

test('example test', async ({ page }) => {
  await page.goto('https://playwright.dev');
  await expect(page).toHaveScreenshot();
});
```
_"Error: A snapshot doesn't exist at example.spec.ts-snapshots/example-test-1-chromium-darwin.png, writing actual."_

----
### Assignment 5
* Create a testcase that takes a screenshot of the login page
* Run it once to generate the snapshot
* Run it a second time, does it pass?

----
### Visual Regression Testing - The Challenges
* Dynamic or volatile elements change often, causing mismatches
    * Apply Custom CSS to hide these elements
    * Mask elements by locator
* Snapshots are unique per browser and operating system.
* Locally generated screenshots (on Windows/macOS) won't pass in (Linux) CI.

---
```ts [] 
import { test, expect } from '@playwright/test';

test('example test', async ({ page }) => {
  await page.goto('https://playwright.dev');
  await expect(page).toHaveScreenshot({ stylePath: path.join(__dirname, 'screenshot.css') });
});
```


```ts []
await page.goto('https://playwright.dev');
await expect(page).toHaveScreenshot({
  mask: [page.locator('img')],
  maskColor: '#00FF00', // green
});
```

----
### Assignment 6
* Mask the volatile element!
* Update the snapshot
* Run it a second time, does your test pass?


----
### Accessability Testing
* Playwright needs an additional library like _Axe_ to run accessability tests. 

```ts []
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright'; // 1

test.describe('homepage', () => { // 2
  test('should not have any automatically detectable accessibility issues', async ({ page }) => {
    await page.goto('https://your-site.com/'); // 3
    const accessibilityScanResults = await new AxeBuilder({ page }).analyze(); // 4
    expect(accessibilityScanResults.violations).toEqual([]); // 5
  });
});
```
NOTE: It scans _current_ state. make sure the page is in the desired state.

----
### Assigment
* Create a testcase that adds a few todos. 
* Once the Todos are created, run an accessability scan with Axe.
* How many violations are there?

---
### CI Integration
* Setting everything up manually
```bash
# Install NPM packages
npm ci

# Install Playwright browsers and dependencies
npx playwright install --with-deps
```
----
* Using official Docker Container

```yml
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
jobs:
  playwright:
    name: 'Playwright Tests'
    runs-on: ubuntu-latest
    container:
      image: mcr.microsoft.com/playwright:v1.41.1-jammy
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Install dependencies
        run: npm ci
      - name: Run your tests
        run: npx playwright test
        env:
          HOME: /root
```
----
### Assigment
* Try and get the Github Actions workflow running!

---
### Linting
* Mistakes are easy to make
* Best practices are hard to implement, even harder to maintain

Linting _can_ help with this. Playwright supplies an ESLint plugin that enforces some best practices
example: Prevent a ``test.only`` from being comitted

* Plugin is _very_ opinionated, might not work well for everyone.

Find it on NPM: ``eslint-plugin-playwright``
