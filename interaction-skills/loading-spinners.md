---
name: loading-spinners
description: Wait for client-side loading spinners that wait_for_load misses
---

# Loading spinner wait pattern

## Problem

`wait_for_load()` checks `document.readyState`. Client-side SPAs often show a loading spinner via JavaScript without changing the document's ready state — `wait_for_load()` returns `True` immediately while the real content is still behind an overlay.

## Solution

After form submissions or navigation that triggers a loading state:

1. Wait 2-3 seconds
2. Check if a spinner/overlay is visible
3. Only proceed when the overlay disappears

```python
wait(3)
# Check if spinner is gone - overlay becomes empty or hidden
spinner = js("document.querySelector('.loading-overlay, [data-testid=\"loading-spinner\"]')?.style?.display !== 'none'")
```

## Sites that do this

- **X.com**: After username submission, a black overlay with a blue spinner appears. Password field only shows after spinner clears (3-5s).
- Most modern SPAs on form submit
- Any "Submitting..." state that blocks the UI

## When `wait_for_load()` works

- Full page navigation (URL change)
- Initial page load
- Navigate to a new route (React Router, Next.js App Router)

## When it doesn't

- In-page state transitions (login steps, tab switches)
- Modal submissions
- AJAX form submits that replace content in-place