An `iframe` with a `script` element that dynamically `import`s a module.

In Chrome, this works as expected. In Firefox 68, this gives:

> TypeError: error loading dynamically imported module

…even though the network request successfully resolves with `200 OK`.

## Building

Run `python3 -m http.server` in the root directory of the repository,
then point a web browser at <http://localhost:8000/>.

Or, visit <https://wchargin.github.io/firefox-iframe-module/>.

## Variants

The module `mod.js` can be successfully imported from the main frame:

```diff
diff --git a/index.html b/index.html
index d3c41be..eae1af1 100644
--- a/index.html
+++ b/index.html
@@ -11,6 +11,7 @@
       const script = subdocument.createElement("script");
       const importPath = String(new URL("mod.js", window.location));
       console.log("Will load from:", importPath);
+      import(importPath);
       script.textContent = `import(${JSON.stringify(importPath)})`;
       subdocument.body.appendChild(script);
     </script>
```

If you add a `debugger;` statement inside the inner frame’s `script`
element, then the Firefox debugger properly stops at the breakpoint;
pressing “Resume” without taking any action causes the module to be
loaded properly!

```diff
diff --git a/index.html b/index.html
index d3c41be..d0f727f 100644
--- a/index.html
+++ b/index.html
@@ -11,7 +11,7 @@
       const script = subdocument.createElement("script");
       const importPath = String(new URL("mod.js", window.location));
       console.log("Will load from:", importPath);
-      script.textContent = `import(${JSON.stringify(importPath)})`;
+      script.textContent = `debugger; import(${JSON.stringify(importPath)})`;
       subdocument.body.appendChild(script);
     </script>
   </body>
```
