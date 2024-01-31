### Basic Usage

- Writing your first test
- Locators & ElementHandles
- Web First Assertions?

- Playwright API, interacting with your App.

----

### Writing your first test

```ts
import { test, expect } from '@playwright/test';

test('has title', async ({ page }) => {
  await page.goto('https://playwright.dev/');

  // Expect a title "to contain" a substring.
  await expect(page).toHaveTitle(/Playwright/);
});
```
> Assignment: Write your first Playwright Test! Use any of the available tools!

Use https://demo.playwright.dev/todomvc/

----
### Locators
Playwright offers two methods for referencing elements
* ElementHandles 👎
* Locators 👍

ElementHandles point to specific elements at a specific point in time. 
When Using Locators, an up-to-date element is fetched every single time you use it.

----
### ElementHandles
```ts
const handle = await page.$('text=Submit');
// ...
await handle.hover();
await handle.click();
```

----
### Locators
```ts
const locator = page.getByText('Submit');
// ...
await locator.hover();
await locator.click();
```

----
### Web First Assertions
* Regular Assertions usually only assert *once*
* Lazy loading or slower websites may present a problem and may result in *flaky* tests.

* Web First assertion(s) continously retry the assertion until the condition is met (or a timeout)
* Input for a web first assertion is a *locator*

----
### Web First Assertion - Example

``` TS
// 👍 Expect a locator to be visible
await expect(page.getByText('welcome')).toBeVisible(); 


// 👎 Expect true/false to be true
expect(await page.getByText('welcome').isVisible()).toBe(true);
```
It is considered a best practice to use web first assertions as much as possible!

----
### Playwright API - Actions
```ts
Text Input: await page.getByRole('textbox').fill('Peter');
checkbox: await page.getByRole('checkbox').setChecked(true); 
Dropdown: await page.getByLabel('Choose a color').selectOption('blue');
Click: await page.getByRole('button').click();
Key Presses: await page.locator('#area').pressSequentially('Hello World!');
Key Pres: await page.getByText('Submit').press('Enter');
Drag & Drop: await page.locator('#item-to-be-dragged').dragTo(page.locator('#item-to-drop-at'));
File upload: await page.getByLabel('Upload file').setInputFiles(path.join(__dirname, 'myfile.pdf'));
```

----
### Playwright API - API Requests
```ts
const REPO = 'test-repo-1';
const USER = 'github-username';

test('should create a bug report', async ({ request }) => {
  const newIssue = await request.post(`/repos/${USER}/${REPO}/issues`, {
    data: {
      title: '[Bug] report 1',
      body: 'Bug description',
    }
  });
  expect(newIssue.ok()).toBeTruthy();
  const JsonResp = await newIssue.json();
  console.log(JsonResp)
});
```
----
### Playwright API - API Requests
* Additional configuration for things like headers and authentication in `playwright.config.ts`
```ts
import { defineConfig } from '@playwright/test';
export default defineConfig({
  use: {
    // All requests we send go to this API endpoint.
    baseURL: 'https://api.github.com',
    extraHTTPHeaders: {
      // We set this header per GitHub guidelines.
      'Accept': 'application/vnd.github.v3+json',
      // Add authorization token to all requests.
      // Assuming personal access token available in the environment.
      'Authorization': `token ${process.env.API_TOKEN}`,
    },
  }
});
```
