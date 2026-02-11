# Lesson 12: Best Practices and Tips

Essential practices for writing robust, maintainable InDesign scripts.

## Code Organization

### 1. Use Descriptive Names

```javascript
// ✗ Bad
var x = doc.pages[0];
var f = x.textFrames.add();

// ✓ Good
var firstPage = doc.pages[0];
var headerFrame = firstPage.textFrames.add();
```

### 2. Add Comments

```javascript
// ✓ Good practice
// Check if document is open
if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

// Get active document
var doc = app.activeDocument;

// Loop through all pages
for (var i = 0; i < doc.pages.length; i++) {
    // Process each page
    var page = doc.pages[i];
}
```

### 3. Use Functions

```javascript
// Break code into functions

function checkDocument() {
    if (app.documents.length == 0) {
        alert("Please open a document!");
        return false;
    }
    return true;
}

function getUserInput(message, defaultValue) {
    var input = prompt(message, defaultValue);
    if (!input) return null;
    return input.trim();
}

function showResults(count, action) {
    alert("Complete!\n" + action + " " + count + " item(s).");
}

// Main script
if (!checkDocument()) exit();

var searchTerm = getUserInput("Search for:", "");
if (!searchTerm) exit();

// ... do work ...

showResults(count, "Processed");
```

## Error Handling

### 1. Always Use Try-Catch

```javascript
// ✓ Wrap risky operations
try {
    var page = doc.pages.itemByName("99");
    // Do something with page
} catch (e) {
    alert("Error: " + e.message);
}
```

### 2. Check Before Accessing

```javascript
// ✓ Check collections before accessing
if (app.documents.length > 0) {
    var doc = app.activeDocument;
}

// ✓ Check if items exist
if (doc.pages.length > 5) {
    var page = doc.pages[5];
}

// ✓ Check for selection
if (app.selection.length > 0) {
    var item = app.selection[0];
}
```

### 3. Validate User Input

```javascript
function getValidNumber(message, defaultVal, min, max) {
    var input = prompt(message, defaultVal);
    
    if (!input) return null; // User canceled
    
    var num = parseFloat(input);
    
    if (isNaN(num)) {
        alert("Invalid number!");
        return null;
    }
    
    if (min != undefined && num < min) {
        alert("Number must be at least " + min);
        return null;
    }
    
    if (max != undefined && num > max) {
        alert("Number must be at most " + max);
        return null;
    }
    
    return num;
}

// Usage
var scale = getValidNumber("Enter scale (50-200):", "100", 50, 200);
if (scale == null) exit();
```

## Performance Optimization

### 1. Disable Screen Redraw

```javascript
// Speed up scripts by preventing screen updates

app.scriptPreferences.enableRedraw = false;

// ... do lots of operations ...

app.scriptPreferences.enableRedraw = true;
app.activeWindow.screenMode = ScreenModeOptions.NORMAL;
```

### 2. Use Undo in Single Step

```javascript
// Wrap entire script in one undo step

var doc = app.activeDocument;

// Start undo group
app.doScript(
    function() {
        // All your operations here
        for (var i = 0; i < doc.pages.length; i++) {
            // ... modifications ...
        }
    },
    ScriptLanguage.JAVASCRIPT,
    [],
    UndoModes.ENTIRE_SCRIPT,
    "My Script Action"
);
```

### 3. Cache Frequently Used Values

```javascript
// ✗ Inefficient - recalculates every iteration
for (var i = 0; i < doc.pages.length; i++) {
    // doc.pages.length is accessed repeatedly
}

// ✓ Efficient - cache the length
var pageCount = doc.pages.length;
for (var i = 0; i < pageCount; i++) {
    var page = doc.pages[i];
}
```

### 4. Access Collections Wisely

```javascript
// ✗ Slow - creates new collection each time
for (var i = 0; i < doc.pages.length; i++) {
    for (var j = 0; j < doc.pages[i].textFrames.length; j++) {
        var frame = doc.pages[i].textFrames[j];
    }
}

// ✓ Faster - cache collections
var pages = doc.pages;
for (var i = 0; i < pages.length; i++) {
    var textFrames = pages[i].textFrames;
    for (var j = 0; j < textFrames.length; j++) {
        var frame = textFrames[j];
    }
}
```

## User Experience

### 1. Provide Feedback

```javascript
// Show progress for long operations

var totalPages = doc.pages.length;

for (var i = 0; i < totalPages; i++) {
    // Update progress every 10 pages
    if (i % 10 == 0) {
        // Note: Can't update progress bar in ExtendScript
        // But can show status in Window title (hack)
        app.activeWindow.name = "Processing: " + i + "/" + totalPages;
    }
    
    // Process page...
}

alert("Complete! Processed " + totalPages + " pages.");
```

### 2. Confirm Destructive Actions

```javascript
// Always confirm before deleting/replacing

var confirm = window.confirm(
    "This will delete " + count + " items.\n" +
    "This cannot be undone.\n\n" +
    "Continue?"
);

if (!confirm) {
    alert("Canceled.");
    exit();
}

// Proceed with deletion...
```

### 3. Provide Clear Messages

```javascript
// ✗ Vague
alert("Error!");

// ✓ Specific
alert("Error: Style 'Heading 1' not found.\nPlease create it first.");

// ✓ Helpful
alert(
    "Script Requirements:\n\n" +
    "1. Document must be open\n" +
    "2. At least one page selected\n" +
    "3. Style 'Body Text' must exist"
);
```

## Script Structure Template

```javascript
// script_template.jsx
// Description: [What this script does]
// Author: [Your name]
// Date: [Creation date]

// ============================================
// CONFIGURATION
// ============================================

var CONFIG = {
    SCRIPT_NAME: "My Script",
    VERSION: "1.0"
};

// ============================================
// MAIN FUNCTION
// ============================================

function main() {
    // 1. Check preconditions
    if (!checkPreconditions()) {
        return;
    }
    
    // 2. Get user input
    var input = getUserInput();
    if (!input) {
        return;
    }
    
    // 3. Confirm action
    if (!confirmAction(input)) {
        return;
    }
    
    // 4. Perform operations
    try {
        var result = performOperations(input);
        showResults(result);
    } catch (e) {
        showError(e);
    }
}

// ============================================
// HELPER FUNCTIONS
// ============================================

function checkPreconditions() {
    if (app.documents.length == 0) {
        alert("Please open a document first!");
        return false;
    }
    return true;
}

function getUserInput() {
    var input = prompt("Enter value:", "");
    if (!input) {
        alert("Canceled.");
        return null;
    }
    return input;
}

function confirmAction(input) {
    return window.confirm(
        "Process with value: " + input + "?\n\n" +
        "Continue?"
    );
}

function performOperations(input) {
    var doc = app.activeDocument;
    var count = 0;
    
    // Disable redraw for performance
    app.scriptPreferences.enableRedraw = false;
    
    try {
        // Your operations here
        // ...
        
    } finally {
        // Always re-enable redraw
        app.scriptPreferences.enableRedraw = true;
    }
    
    return count;
}

function showResults(count) {
    alert(
        CONFIG.SCRIPT_NAME + " v" + CONFIG.VERSION + "\n\n" +
        "Complete!\n" +
        "Processed: " + count + " item(s)"
    );
}

function showError(error) {
    alert(
        "Error occurred:\n\n" +
        error.message + "\n\n" +
        "Line: " + error.line
    );
}

// ============================================
// RUN SCRIPT
// ============================================

main();
```

## Debugging Techniques

### 1. Use $.write() for Logging

```javascript
// Output to ExtendScript Toolkit console
$.writeln("Starting script...");
$.writeln("Page count: " + doc.pages.length);

// Or use for debugging
$.writeln("DEBUG: Variable value = " + myVar);
```

### 2. Break Complex Expressions

```javascript
// ✗ Hard to debug
var result = doc.pages[0].textFrames[getUserInput()].contents;

// ✓ Easy to debug
var pageNum = getUserInput();
$.writeln("Page number: " + pageNum);

var page = doc.pages[0];
$.writeln("Got page");

var frameIndex = pageNum;
var frame = page.textFrames[frameIndex];
$.writeln("Got frame");

var result = frame.contents;
$.writeln("Got contents: " + result);
```

### 3. Test Incrementally

```javascript
// Test each section independently

// Test 1: Can we access document?
// alert("Test 1: " + app.activeDocument.name);

// Test 2: Can we get pages?
// alert("Test 2: Pages = " + app.activeDocument.pages.length);

// Test 3: Can we loop pages?
// for (var i = 0; i < app.activeDocument.pages.length; i++) {
//     alert("Test 3: Page " + i);
// }

// Uncomment tests as needed
```

## Common Patterns

### Pattern 1: Process All Items of Type

```javascript
function processAllTextFrames(callback) {
    var doc = app.activeDocument;
    var frames = doc.textFrames;
    
    for (var i = 0; i < frames.length; i++) {
        callback(frames[i], i);
    }
}

// Usage
processAllTextFrames(function(frame, index) {
    frame.contents = "Frame " + index;
});
```

### Pattern 2: Safe Style Access

```javascript
function getOrCreateStyle(doc, styleName, styleType) {
    var collection = doc[styleType + "Styles"];
    
    try {
        var style = collection.item(styleName);
        style.name; // Test if exists
        return style;
    } catch (e) {
        // Doesn't exist, create it
        var newStyle = collection.add();
        newStyle.name = styleName;
        return newStyle;
    }
}

// Usage
var heading = getOrCreateStyle(doc, "Heading 1", "paragraph");
```

### Pattern 3: Safe Find/Replace

```javascript
function safeFind(doc, findType, findPrefs, changePrefs) {
    // Clear preferences
    app["find" + findType + "Preferences"] = NothingEnum.NOTHING;
    app["change" + findType + "Preferences"] = NothingEnum.NOTHING;
    
    // Set preferences
    for (var key in findPrefs) {
        app["find" + findType + "Preferences"][key] = findPrefs[key];
    }
    
    if (changePrefs) {
        for (var key in changePrefs) {
            app["change" + findType + "Preferences"][key] = changePrefs[key];
        }
    }
    
    // Perform find
    var found = doc["find" + findType]();
    
    // Clear preferences
    app["find" + findType + "Preferences"] = NothingEnum.NOTHING;
    app["change" + findType + "Preferences"] = NothingEnum.NOTHING;
    
    return found;
}

// Usage
var results = safeFind(doc, "Text", { findWhat: "hello" });
```

## Script Distribution

### 1. Add Header Comments

```javascript
/*
 * Script Name: Amazing Script
 * Author: Your Name
 * Version: 1.0
 * Date: 2026-02-10
 * 
 * Description:
 * This script does amazing things with your InDesign document.
 * 
 * Requirements:
 * - InDesign CS6 or later
 * - Document must be open
 * 
 * Usage:
 * 1. Open your document
 * 2. Run the script
 * 3. Follow the prompts
 * 
 * License: MIT
 */
```

### 2. Version Checking

```javascript
// Check InDesign version
var minVersion = 9.0; // CS6
var currentVersion = parseFloat(app.version);

if (currentVersion < minVersion) {
    alert("This script requires InDesign CS6 or later.");
    exit();
}
```

### 3. Include Instructions

Create a README.txt with your script:

```
INSTALLATION
============
1. Copy script_name.jsx to Scripts Panel folder
2. Restart InDesign
3. Access via Window > Utilities > Scripts

USAGE
=====
1. Open document
2. Double-click script
3. Follow prompts

TROUBLESHOOTING
===============
- Error "No document": Open a document first
- Error "Style not found": Create required styles
- Script hangs: Contact support with details
```

## Next Steps

- Review [Debugging](13-debugging.md) for troubleshooting
- Check [Quick Reference](14-quick-reference.md) for syntax
- See [Resources](15-resources.md) for more learning

## Key Takeaways

✓ Use descriptive names and comments  
✓ Always validate input and check conditions  
✓ Wrap operations in try-catch blocks  
✓ Disable redraw for performance  
✓ Cache frequently accessed values  
✓ Provide clear user feedback  
✓ Confirm destructive actions  
✓ Test incrementally during development  
✓ Include documentation with scripts  
