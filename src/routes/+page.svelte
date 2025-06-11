<script>
  import { onMount } from 'svelte';
  import { enhance } from '$app/forms';

  onMount(() => {
    // Set a hash that contains invalid CSS selector characters
    // This simulates gzipped data in URL hash which is a common pattern
    location.hash = '#gzip:H4sIAAAAAAAAA0sqTQcAFL+MNQMAAAA=';
  });

  function handleSubmit() {
    // This form submission will trigger SvelteKit's reset_focus function
    // which will try to call document.querySelector(location.hash)
    // and fail with "SyntaxError: '#gzip:...' is not a valid selector"
    console.log('Form submitted, reset_focus will be triggered');
  }
</script>

<h1>SvelteKit reset_focus bug reproduction</h1>

<p>Steps to reproduce:</p>
<ol>
  <li>Page loads with invalid CSS selector in URL hash (#gzip:...)</li>
  <li>Submit the form below</li>
  <li>SvelteKit's reset_focus function will call document.querySelector(location.hash)</li>
  <li>This throws: SyntaxError: '#gzip:...' is not a valid selector</li>
</ol>

<form method="POST" use:enhance on:submit={handleSubmit}>
  <input name="test" placeholder="Test input" />
  <button type="submit">Submit (triggers reset_focus)</button>
</form>

<p>Current hash: <code>{typeof window !== 'undefined' ? window.location.hash : ''}</code></p>

<p>
  This pattern is common for apps that store state in URL hash,
  such as gzipped data, base64 encoded data, or other formats that
  are not valid CSS selectors.
</p>
