## Memory Leak

There are usually 4 type of leaks

- Detached DOM tree: DOM node is removed from DOM tree, but Javascript still references it.
- Detached window: The popup window was closed, but our code (main page) has a reference to it.
- Accidental global variable: GC doesn't claim back a global variable except we explicitly force a browser to do (via setting it `null/undefined`).
- Event listener: a browser only remove an event listener if either you explicitly remove it (`removeEventListener`) or the associated DOM element is removed. That listener function is kept there together with what it keeps in its scope.
- Timer.

### Using Chrome

- Clicking on `More tools > Task Manager` to view how memory usage.

  ![memory usage](https://i.imgur.com/08Gobio.png)

- Clicking on `Devtools > Memory` to profile.

For example: using this page

```html
<html>
  <body>
    <button id="grow">Test</button>
    <script>
      var x = [];
      function grow() {
        x.push(new Array(1000000).join("x"));
      }

      document.getElementById("grow").addEventListener("click", grow);
    </script>
  </body>
</html>
```

Clicking Test button, we will see a spike in timeline. Narrowing that spike, we can check which one are potential issues.
![start](https://i.imgur.com/BBHkeYe.png)
![end](https://i.imgur.com/S9Z2s5P.png)

### References

- https://developer.chrome.com/docs/devtools/memory-problems/
- https://web.dev/detached-window-memory-leaks/
