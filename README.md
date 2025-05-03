# Branches.js
## a simple framework for embedding different data inside an html

**File:** `branches3.js`  
**Path:** `c:\Github\42knotsFull\pub\js\branches3.js`  
**Author:** Guy Louzon  
**License:** GNU 3  
**Repository:** [https://www.github.com/GuyLou/branches.js/](https://www.github.com/GuyLou/branches.js/)

---

## Overview

The `branches` class provides a templating solution for rendering dynamic HTML content. It supports two modes:
- **Branches mode:** For processing static templates embedded in the DOM.
- **Leaves mode:** For processing templates that represent repeated elements (lists).

This class is responsible for:
- Initializing default settings.
- Extracting and storing template strings with token placeholders.
- Replacing tokens (formatted as `{$token}`) with provided values.
- Rendering the final HTML into the DOM.

---

## Class Structure

### Constructor

```javascript
constructor(params = {})
```

- **Description:**  
  Initializes a new instance of the `branches` class.
  
- **Parameters:**  
  - `params`: An optional object supporting:
    - `masterNode`: Selector or actual DOM element to work on.  
      - If a string is provided, branching logic determines whether it is an `id` (e.g., `'#myId'`), a `class` (e.g., `'.myClass'`), or a tag name.
    - `masterClass`: A class name for elements that form the foundation for templating (default: `"branches"`).
    - `type`: Either `"branches"` (default) or `"leaves"`.  
      - `"leaves"` mode initializes a `leaves` object and processes the contained templates.
    - `tmplt`: A template string to use instead of innerHTML.
  
- **Example:**
```javascript
const b = new branches({
  masterNode: '#container',
  masterClass: 'myTemplate',
  type: 'leaves',
  tmplt: '<div>{$content}</div>'
});
```

---

### Methods

#### classDefaults()

```javascript
classDefaults()
```

- **Description:**  
  Sets the default configuration for the instance. Default values include:
  - `type`: `"branches"`
  - `masterClass`: `"branches"`
  - `tmplt`: `""`
  - `tmplt_url`: `""`
  
- **Usage:**  
  Called automatically in the constructor.

---

#### initBranches()

```javascript
initBranches()
```

- **Description:**  
  Scans the DOM (starting at `masterNode`) for elements with the class name specified by `masterClass`. For each found element:
  - Extracts its `innerHTML` as a template.
  - Matches tokens in the format `{$token}`.
  - Inserts a comment containing the original template for later reference.
  - Processes attributes containing tokens.

---

#### plantHTML(key, value)

```javascript
plantHTML(key, value)
```

- **Description:**  
  Finds all elements with a class name of `branch_html_{key}` and replaces occurrences of the token `{$key}` with the given `value` in their inner HTML.
  
- **Example:**
```javascript
branchesInstance.plantHTML("title", "Dynamic Title");
```

---

#### plantAttrib(key, value)

```javascript
plantAttrib(key, value)
```

- **Description:**  
  Replaces tokens in HTML attributes. Finds elements having a class of `branch_attrib_{key}` and replaces the token `{$key}` within the stored attribute value with the provided `value`.

---

#### climbABranch(bran)

```javascript
climbABranch(bran)
```

- **Description:**  
  Iterates over all keys in object `bran` and calls both `plantHTML` and `plantAttrib` for each key/value pair.
  
- **Example:**
```javascript
branchesInstance.climbABranch({ title: "New Title", subheading: "Subtitle text" });
```

---

#### initLeaves() and initLeaf(tmplt, leafId)

- **initLeaves():**  
  Processes DOM elements with `masterClass` and calls `initLeaf` for each to store:
  - `leafId`: The element's `id`.
  - `tmplt`: The element’s original inner HTML.
  - `vars`: An array of token strings matched in the template.

- **initLeaf(tmplt, leafId):**  
  Stores a single leaf’s template details in the instance’s `leaves` object.

- **Example:**
```javascript
// For an element with id="leaf1" containing '<p>{$greeting}</p>'
branchesInstance.initLeaf(document.getElementById("leaf1").innerHTML, "leaf1");
```

---

#### sprout(leafId, params)

```javascript
sprout(leafId, params)
```

- **Description:**  
  Processes a stored leaf template by replacing tokens with values from the `params` object.  
- **Returns:**  
  A processed HTML string.
  
- **Example:**
```javascript
const output = branchesInstance.sprout("leaf1", { greeting: "Hello, World!" });
```

---

#### generateLeaves(leafId, itemsjson)

```javascript
generateLeaves(leafId, itemsjson)
```

- **Description:**  
  Iterates over a JSON data collection and calls `sprout()` for each item using the provided leaf template. Aggregates the results into a single HTML string.
  
- **Usage:**
```javascript
const htmlString = branchesInstance.generateLeaves("leaf1", jsonData);
```

---

#### leavesLuv(leafId, itemsjson, append = 1)

```javascript
leavesLuv(leafId, itemsjson, append = 1)
```

- **Description:**  
  Generates HTML from JSON data using `generateLeaves` and renders it into the DOM element with the specified `leafId`. If `append` is set to `1`, the generated HTML is appended to existing content; otherwise, it replaces it.
  
- **Example:**
```javascript
branchesInstance.leavesLuv("leaf1", jsonItems, 1);
```

---

#### renderTemplate(elemId, filler, append = 1)

```javascript
renderTemplate(elemId, filler, append = 1)
```

- **Description:**  
  Inserts the final HTML (stored in the `filler` string) into the DOM element identified by `elemId`.  
  - If `append` is `1`, existing innerHTML is preserved; otherwise, it is replaced.
  
- **Example:**
```javascript
branchesInstance.renderTemplate("elementId", "<p>Updated Content</p>", 0);
```

---

## Example Usage

Below is an example of using the `branches` class to process and render a simple template:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Branches Example</title>
  <script src="branches3.js"></script>
</head>
<body>
  <!-- Container for leaves -->
  <div id="leafContainer" class="branches">
    <div id="leaf1" class="template">
      <p>{$greeting}</p>
    </div>
  </div>

  <script>
    // Initialize branches for leaves processing
    const myBranches = new branches({ type: 'leaves', masterClass: 'branches' });
    // Initialize the leaf from the innerHTML of the element with id 'leaf1'
    myBranches.initLeaf(document.getElementById("leaf1").innerHTML, "leaf1");
    // Process the template by replacing {$greeting} with a custom message
    const processedHTML = myBranches.sprout("leaf1", { greeting: "Hello, Branches!" });
    // Render the processed HTML into the element with id 'leaf1'
    myBranches.renderTemplate("leaf1", processedHTML, 0);
  </script>
</body>
</html>
```

---

## License

This file is distributed under the GNU General Public License v3.  
For more details, see the repository: [https://www.github.com/GuyLou/branches.js/](https://www.github.com/GuyLou/branches.js/)

---
