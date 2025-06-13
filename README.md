# SvelteKit reset_focus Bug Reproduction

This repository demonstrates a [bug](https://github.com/sveltejs/kit/issues/13883) in SvelteKit where the `reset_focus` function crashes when the URL contains a hash fragment that is not a valid CSS selector.

## Bug Description

SvelteKit's internal `reset_focus` function calls `document.querySelector(location.hash)` without validating that the hash is a valid CSS selector. This causes a `SyntaxError` when the hash contains data that doesn't conform to CSS selector syntax.

**Error message:**

```
SyntaxError: '#gzip:H4sIAAAAAAAAA0sqTQcAFL+MNQMAAAA=' is not a valid selector.
```

## Common Use Cases Affected

This bug affects applications that use URL hash fragments for:

- Storing compressed/gzipped state data
- Base64 encoded application data
- Any data format that contains characters invalid in CSS selectors (like `:`, `+`, `/`, etc.)

## Reproduction Steps

1. Clone this repository
2. Install dependencies: `npm install`
3. Start the development server: `npm run dev`
4. Open the application in your browser
5. Submit the form on the page
6. Check the browser console for the error

## What Happens

1. The page loads and sets `location.hash` to a gzipped data string starting with `#gzip:`
2. When the form is submitted, SvelteKit's `use:enhance` triggers the `reset_focus` function
3. `reset_focus` attempts to call `document.querySelector(location.hash)`
4. Since `#gzip:...` is not a valid CSS selector, `querySelector` throws a `SyntaxError`
5. This crashes the focus management functionality

## Expected Behavior

SvelteKit should either:

1. Validate that the hash is a valid CSS selector before calling `querySelector`
2. Wrap the `querySelector` call in a try-catch block to handle invalid selectors gracefully
3. Check if the selector actually matches an element before proceeding

## Impact

This bug can crash the focus management in applications that legitimately use URL hash fragments for data storage, which is a common pattern in modern single-page applications.
