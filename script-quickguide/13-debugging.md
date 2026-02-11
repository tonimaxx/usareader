# Lesson 13: Debugging and Troubleshooting

Learn how to identify and fix common InDesign scripting issues.

## Understanding Error Messages

### Error Number: 24
**"xxx is not a function"**

This occurs when using modern JavaScript methods not supported in ExtendScript.

```javascript
// ✗ Causes Error 24
var numbers = [1, 2, 3];
var doubled = numbers.map(function(n) { return n * 2; });

// ✓ Solution: Use traditional loops
var numbers = [1, 2, 3];
var doubled = [];
for (var i = 0; i < numbers.length; i++) {
    doubled.push(numbers[i] * 2);
}
```

### Error: "Object is Invalid"

Occurs when trying to access a deleted or non-existent object.

```javascript
// ✗ Problem
var page = doc.pages[10]; // Page doesn't exist
page.name; // Error!

// ✓ Solution: Check first
if (doc.pages.length > 10) {
    var page = doc.pages[10];
} else {
    alert("Page 10 doesn't exist");
}
```

### Error: "Cannot find element"

Trying to access by name that doesn't exist.

```javascript
// ✗ Problem
var style = doc.paragraphStyles.item("Heading");
style.name; // Throws error if doesn't exist

// ✓ Solution: Use try-catch
try {
    var style = doc.paragraphStyles.item("Heading");
    style.name; // Test if exists
    // Use style...
} catch (e) {
    alert("Style 'Heading' not found!");
}
```

### TypeError: undefined is not an object

Variable is not defined or null.

```javascript
// ✗ Problem
var doc = app.activeDocument; // No document open
alert(doc.name); // Error!

// ✓ Solution: Check first
if (app.documents.length == 0) {
    alert("No document open!");
    exit();
}
var doc = app.activeDocument;
```

## Common Issues and Solutions

### Issue 1: Script Won't Run

**Symptoms:**
- Double-clicking does nothing
- No error message

**Solutions:**

```javascript
// 1. Check file extension
// Must be .jsx not .js

// 2. Check for syntax errors
// Missing brackets, semicolons, etc.

// 3. Add basic test
alert("Script started!");

// 4. Check script location
// Must be in Scripts Panel folder
```

### Issue 2: "Variable is not defined"

**Cause:** Using variable before declaring it.

```javascript
// ✗ Problem
alert(myVar); // Error: myVar is not defined
var myVar = 10;

// ✓ Solution: Declare first
var myVar = 10;
alert(myVar);
```

### Issue 3: Script Hangs/Freezes

**Cause:** Infinite loop or very long operation.

```javascript
// ✗ Problem - Infinite loop
var i = 0;
while (i < 10) {
    // Forgot to increment i
    alert(i);
}

// ✓ Solution: Ensure loop exits
var i = 0;
while (i < 10) {
    alert(i);
    i++; // Don't forget!
}

// For long operations, add progress feedback
for (var i = 0; i < 1000; i++) {
    if (i % 100 == 0) {
        $.writeln("Progress: " + i);
    }
    // ... work ...
}
```

### Issue 4: Wrong Results

**Cause:** Logic errors or incorrect assumptions.

```javascript
// ✗ Problem - Off by one
for (var i = 1; i <= doc.pages.length; i++) {
    var page = doc.pages[i]; // Error on last page!
}

// ✓ Solution: Arrays are zero-indexed
for (var i = 0; i < doc.pages.length; i++) {
    var page = doc.pages[i];
}
```

### Issue 5: String/Number Confusion

**Cause:** JavaScript's loose typing.

```javascript
// ✗ Problem - String concatenation instead of addition
var a = "10";
var b = "20";
var sum = a + b; // "1020" not 30

// ✓ Solution: Parse to numbers
var a = parseInt("10");
var b = parseInt("20");
var sum = a + b; // 30

// Or use Number()
var sum = Number("10") + Number("20");
```

## Debugging Techniques

### 1. Console Logging

```javascript
// Output to ExtendScript Toolkit console
$.writeln("Debug: Starting script");
$.writeln("Page count: " + doc.pages.length);

// Check variable values
var myVar = calculateSomething();
$.writeln("Result: " + myVar);
```

### 2. Alert Debugging

```javascript
// Show values at different points
alert("Step 1: Document opened");

var pages = doc.pages.length;
alert("Step 2: Found " + pages + " pages");

for (var i = 0; i < pages; i++) {
    alert("Step 3: Processing page " + i);
}

alert("Step 4: Complete!");
```

### 3. Try-Catch with Details

```javascript
try {
    // Your code here
    var page = doc.pages[100];
    
} catch (e) {
    // Detailed error report
    var errorReport = "ERROR DETAILS:\n\n";
    errorReport += "Message: " + e.message + "\n";
    errorReport += "Line: " + e.line + "\n";
    errorReport += "File: " + e.fileName + "\n";
    errorReport += "Number: " + e.number + "\n";
    
    alert(errorReport);
    $.writeln(errorReport);
}
```

### 4. Breakpoint Debugging

```javascript
// In ExtendScript Toolkit, use $.bp() to break
function myFunction() {
    var result = 0;
    
    $.bp(); // Debugger will pause here
    
    for (var i = 0; i < 10; i++) {
        result += i;
    }
    
    return result;
}
```

### 5. Step-by-Step Testing

```javascript
// Comment out sections to isolate issues

function processDocument() {
    // Test 1: Can we get document?
    var doc = app.activeDocument;
    alert("Test 1: Got document");
    // return; // Uncomment to test just this
    
    // Test 2: Can we get pages?
    var pages = doc.pages;
    alert("Test 2: Pages = " + pages.length);
    // return; // Uncomment to test up to here
    
    // Test 3: Can we process pages?
    for (var i = 0; i < pages.length; i++) {
        var page = pages[i];
        alert("Test 3: Page " + i);
    }
}
```

### 6. Validation Functions

```javascript
// Create helper to validate state
function validateState(doc) {
    var errors = [];
    
    if (doc.pages.length == 0) {
        errors.push("No pages in document");
    }
    
    if (doc.layers.length == 0) {
        errors.push("No layers in document");
    }
    
    try {
        doc.paragraphStyles.item("Body Text").name;
    } catch (e) {
        errors.push("Missing 'Body Text' style");
    }
    
    if (errors.length > 0) {
        alert("Validation Errors:\n\n" + errors.join("\n"));
        return false;
    }
    
    return true;
}

// Use before processing
if (!validateState(doc)) {
    exit();
}
```

## Performance Debugging

### Issue: Script is Too Slow

**Solution 1: Disable Redraw**
```javascript
// Before
for (var i = 0; i < 100; i++) {
    // Each change redraws screen
    doc.pages[i].appliedMaster = master;
}

// After - Much faster
app.scriptPreferences.enableRedraw = false;
for (var i = 0; i < 100; i++) {
    doc.pages[i].appliedMaster = master;
}
app.scriptPreferences.enableRedraw = true;
```

**Solution 2: Cache Values**
```javascript
// Before - Slow
for (var i = 0; i < doc.pages.length; i++) {
    for (var j = 0; j < doc.pages[i].textFrames.length; j++) {
        // Collections accessed repeatedly
    }
}

// After - Faster
var pages = doc.pages;
var pageCount = pages.length;

for (var i = 0; i < pageCount; i++) {
    var frames = pages[i].textFrames;
    var frameCount = frames.length;
    
    for (var j = 0; j < frameCount; j++) {
        var frame = frames[j];
    }
}
```

**Solution 3: Minimize Operations**
```javascript
// Before - Inefficient
for (var i = 0; i < doc.textFrames.length; i++) {
    var frame = doc.textFrames[i];
    frame.contents = processText(frame.contents);
}

// After - Process in batch
var allText = [];
for (var i = 0; i < doc.textFrames.length; i++) {
    allText.push(doc.textFrames[i].contents);
}

var processed = processAllText(allText);

for (var i = 0; i < doc.textFrames.length; i++) {
    doc.textFrames[i].contents = processed[i];
}
```

## Testing Strategies

### 1. Test on Copies

```javascript
// Always work on copies for testing
if (doc.modified) {
    var confirm = window.confirm(
        "Document has unsaved changes.\n" +
        "Save a copy before running script?"
    );
    
    if (confirm) {
        var backup = File(doc.fullName.replace(".indd", "_backup.indd"));
        doc.save(backup);
    }
}
```

### 2. Start Small

```javascript
// Test with limited data first
var TEST_MODE = true;
var maxPages = TEST_MODE ? 2 : doc.pages.length;

for (var i = 0; i < maxPages; i++) {
    // Process pages
}

if (TEST_MODE) {
    alert("TEST MODE: Processed " + maxPages + " pages only");
}
```

### 3. Dry Run Mode

```javascript
var DRY_RUN = true;

function deleteOldPages() {
    var toDelete = [];
    
    for (var i = 0; i < doc.pages.length; i++) {
        if (isOldPage(doc.pages[i])) {
            toDelete.push(i);
        }
    }
    
    if (DRY_RUN) {
        alert("DRY RUN: Would delete " + toDelete.length + " pages:\n" + toDelete.join(", "));
    } else {
        // Actually delete
        for (var i = toDelete.length - 1; i >= 0; i--) {
            doc.pages[toDelete[i]].remove();
        }
    }
}
```

## Common Gotchas

### 1. Collections vs Arrays

```javascript
// Collections look like arrays but aren't
var pages = doc.pages;

// ✗ These don't work
pages.map(function(p) { return p.name; });
pages.forEach(function(p) { alert(p.name); });

// ✓ Use traditional loops
for (var i = 0; i < pages.length; i++) {
    alert(pages[i].name);
}
```

### 2. Object Comparisons

```javascript
var layer1 = doc.layers[0];
var layer2 = doc.layers[0];

// ✗ This is false (different object instances)
if (layer1 == layer2) { }

// ✓ Compare properties
if (layer1.id == layer2.id) { }
if (layer1.name == layer2.name) { }
```

### 3. Geometric Bounds Order

```javascript
// Remember: [y1, x1, y2, x2] NOT [x1, y1, x2, y2]

// ✗ Wrong order
frame.geometricBounds = [100, 50, 200, 150]; // x, y, x, y?

// ✓ Correct order
frame.geometricBounds = [
    50,   // top (y1)
    100,  // left (x1)
    150,  // bottom (y2)
    200   // right (x2)
];
```

### 4. Units Matter

```javascript
// Default is points (1/72 inch)
frame.geometricBounds = [72, 72, 144, 144]; // 1" square

// Can use unit strings
frame.geometricBounds = ["1in", "1in", "2in", "2in"];

// But can't mix
frame.geometricBounds = [72, "1in", 144, "2in"]; // Error!
```

### 5. Modified Flag

```javascript
// InDesign marks document as modified after script
doc.modified; // true after changes

// To prevent "save changes?" dialog
doc.saved = true; // Mark as saved (use carefully!)
```

## Emergency Fixes

### Script Won't Stop

1. **Force Quit InDesign**
   - macOS: Cmd+Option+Esc
   - Windows: Ctrl+Alt+Delete

2. **Remove Script from Startup**
   - Check Scripts Panel > Startup Scripts

### Script Corrupted Document

1. **Revert to last saved**
   - File > Revert

2. **Use backup**
   - Always keep backups before testing

3. **Undo**
   - Edit > Undo (if possible)

### Can't Find Error

1. **Binary search method**
   ```javascript
   // Comment out half the code
   // If error gone, it's in commented section
   // If error persists, it's in active section
   // Repeat until found
   ```

2. **Add checkpoints**
   ```javascript
   alert("Checkpoint 1");
   // ... code ...
   alert("Checkpoint 2");
   // ... code ...
   alert("Checkpoint 3");
   // Find which checkpoint doesn't appear
   ```

## Script Template with Debugging

```javascript
// debug_template.jsx

// Configuration
var DEBUG_MODE = true;
var TEST_MODE = true;

// Debug logging
function debug(message) {
    if (DEBUG_MODE) {
        $.writeln("[DEBUG] " + message);
        // Optionally also alert
        // alert("[DEBUG] " + message);
    }
}

// Main function
function main() {
    debug("Script started");
    
    // Check document
    if (app.documents.length == 0) {
        alert("No document open!");
        return;
    }
    
    var doc = app.activeDocument;
    debug("Got document: " + doc.name);
    
    // Your code here
    try {
        debug("Starting processing...");
        
        var result = processDocument(doc);
        
        debug("Processing complete. Result: " + result);
        
        if (TEST_MODE) {
            alert("TEST MODE: Would process " + result + " items");
        } else {
            alert("Processed " + result + " items");
        }
        
    } catch (e) {
        var errorMsg = "ERROR:\n";
        errorMsg += "Message: " + e.message + "\n";
        errorMsg += "Line: " + e.line + "\n";
        
        debug(errorMsg);
        alert(errorMsg);
    }
    
    debug("Script finished");
}

function processDocument(doc) {
    debug("Processing document...");
    
    var count = 0;
    var limit = TEST_MODE ? 2 : doc.pages.length;
    
    for (var i = 0; i < limit; i++) {
        debug("Processing page " + i);
        // Do work
        count++;
    }
    
    return count;
}

// Run script
main();
```

## Getting Help

### Resources

1. **Adobe Forums**
   - https://community.adobe.com/t5/indesign/ct-p/ct-indesign

2. **InDesign Secrets**
   - https://indesignsecrets.com/topic/scripting

3. **Stack Overflow**
   - Tag: `extendscript` and `adobe-indesign`

4. **Documentation**
   - InDesign Scripting Guide (PDF from Adobe)
   - JavaScript Tools Guide

### Asking for Help

When posting questions, include:

```javascript
// 1. InDesign version
alert("InDesign " + app.version);

// 2. Code that fails (minimal example)
// 3. Exact error message
// 4. What you expected
// 5. What you tried
```

## Next Steps

- Review [Best Practices](12-best-practices.md)
- Check [Quick Reference](14-quick-reference.md)
- Explore [Resources](15-resources.md)

## Key Takeaways

✓ Use try-catch for error handling  
✓ Check objects exist before accessing  
✓ Use $.writeln() for debugging output  
✓ Test on copies, start small  
✓ Remember ExtendScript is ES3 (old JavaScript)  
✓ Collections aren't true arrays  
✓ Compare object properties, not objects  
✓ Geometric bounds: [y1, x1, y2, x2]  
✓ Keep backups before testing scripts  
