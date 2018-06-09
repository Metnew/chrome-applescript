# Execute AppleScript using Chrome

#### [Original post on Medium](https://medium.com/@vladimirmetnew/i-give-you-a-working-exploit-for-stable-chrome-on-mac-8ac49af40910)

Attacker could trick user to run downloaded `.applescript` file and execute any code in "Script Editor" app with user-level privileges.

Chromium tracker: https://bugs.chromium.org/p/chromium/issues/detail?id=850261

## Attack scenario

1.  Open `exploit.html`

2.  Open new window with `applescript:` url. this will prompt user to open Script Editor application. (skip this step, if we've already got this permission)

3.  User accepts navigation to `applescript:` and grants access to start script editor. User have to click on the checkbox “Always open these type of links in the associated app”.

4.  Script editor opens in background, because of closing tab (Chrome gets focus again, Script Editor is only visible in Dock for now)

5.  Download `exploit.applescript` file. File will be downloaded, because danger level for `.applescript` is not `ALLOW_ON_USER_GESTURE`. [download protection bypass]

6.  At this point, let's assume that user opens file

7.  As of now, file is opened, and we need to execute it. in script editor, "Run" command has a shortcut "cmd + R"

8.  When Script editor opens with our `exploit.applescript`, Chrome lost focus and exploit file content could became visible to user in Script Editor. [but it won't be visible, step 8]

9.  We could make window focused again calling `alert()`. In this case, `exploit.html` will be focused again.

10. At this point, we have Script Editor with exploit inside (moreover, user didn't see this window) and focused `exploit.html` in Chrome.

11. Now we need to trick user into pressing Cmd + R while navigating to `applescript:` url.
    It could be possible with a technique similar to https://bugs.chromium.org/p/chromium/issues/detail?id=637098, when user starts pressing specific keys in browser, but they get evaluated in non-browser windows

12. At this point, user has to press Cmd and then press R. on Cmd keypress, we open `applescript:` url. Script Editor becomes focused, keypress for Cmd is interpreted as keypress on Script Editor and keydown for R will run Script Editor's shortcut "Cmd+R", e.g. "Run". (in attached exploit it's required to press CMD and hit "R" twice)

13. Script Editor runs our `exploit.applescript`.

### Problems

- User should allow `applescript:` open Script Editor by default.

- User has to open file using downloads toolbar. But file is treated as a non-dangerous. Additionally, Script Editor is a default app for `.applescript/.scpt/.scptd` files, so the only required action from user - press file's tab in "downloads" toolbar.

- User has to press a specific shortcut(Cmd + R ) after navigation to `applescript:`.
  That's not a problem too. Cmd + R is a well-known shortcut.

- Exploit doesn’t work in fullscreen mode (i.e. when Chrome’s window is fullscreen, not `element.requestFullScreen()`).

### Impact

Attacker could trick user to execute downloaded applescript file with user-level privilege using Chrome.

### Other browsers

Safari shows alert every time navigation to `applescript:` url occurs.
TL;DR, it's not impossible to silently execute applescript in Safari.

Mozilla: looks like there is no silent auto download in mozilla for `.applescript` files. So it will not work too.

### Version

Chrome Version: 67.0.3396.62 Stable + 69 Canary
Operating System: macOS 10.13.4 17E202 x86_64

### PoC

PoC (exploit, not test case) attached.

### Author

Vladimir Metnew <mailto:vladimirmetnew@gmail.com>
