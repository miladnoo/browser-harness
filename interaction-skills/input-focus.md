---
name: input-focus
description: Framework inputs need click-to-focus before type_text works
---

# Input focus pattern

## Problem

`type_text()` fails silently on React/Vue/Svelte inputs that manage their own state. The text appears to be sent but the input's internal value doesn't update because the input lacks focus.

## Solution

**Always `click()` the input element before `type_text()`** when dealing with modern JavaScript frameworks.

```python
# Wrong - text goes nowhere
type_text("username")

# Correct - focus first, then type
click(x, y)  # coordinates of the input
wait(0.5)
type_text("username")
```

## When this applies

- React inputs with `onChange` handlers
- Vue `v-model` inputs
- Any input inside a shadow DOM or React portal
- X.com login fields
- Most SPAs with custom form state management

## How to detect

If `js("document.querySelector('selector').value")` returns `""` immediately after `type_text()`, the input likely wasn't focused. Click first.

## Alternative: JS dispatch

If coordinate clicks are unreliable, use `js()` to set value and dispatch events:

```python
js("""
(() => {
  const input = document.querySelector('selector');
  if (input) {
    input.focus();
    input.value = 'text';
    input.dispatchEvent(new Event('input', { bubbles: true }));
  }
})()
""")
```