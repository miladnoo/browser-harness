# Connection & Tab Visibility

## Startup sequence

1. Check if a daemon is already running with `daemon_alive()` before starting a new one
2. If stale socket files exist but the daemon is dead, clean them up
3. List open tabs with `list_tabs(include_chrome=True)` to see what's available
4. Attach to a real tab with `ensure_real_tab()`
5. Bring the tab to front with `Target.activateTarget` so the user can see it

```python
# Check existing state first
if daemon_alive():
    print("daemon running")
else:
    # Clean stale sockets if needed
    import os
    for f in ["/tmp/bu-default.sock", "/tmp/bu-default.pid"]:
        if os.path.exists(f): os.unlink(f)
    ensure_daemon()

# See what tabs exist
tabs = list_tabs(include_chrome=True)
for t in tabs:
    print(t["targetId"][:12], t["url"][:60])

# Attach to a real page and make it visible
tab = ensure_real_tab()
cdp("Target.activateTarget", targetId=tab["targetId"])
```

## Tab visibility

`ensure_real_tab()` switches the CDP session but does not bring the tab to front in Chrome's UI. `Target.activateTarget` does that.

The daemon often attaches to `chrome://omnibox-popup.top-chrome/` which has a 1px viewport and is invisible to the user.

## Navigating to a new URL

Prefer navigating an existing visible tab over creating new ones. Tabs created via `new_tab()` may end up in detached windows or behind other tabs.

```python
tab = ensure_real_tab()
cdp("Target.activateTarget", targetId=tab["targetId"])
goto("https://example.com")
```

## Bringing Chrome to front

If the user can't see Chrome at all (window behind other apps or on another desktop):

```python
import subprocess
subprocess.run(["osascript", "-e", 'tell application "Google Chrome" to activate'])
```
