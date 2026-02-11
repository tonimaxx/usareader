# Lesson 1: Getting Started with InDesign Scripting

## What is InDesign Scripting?

InDesign scripting allows you to automate tasks, manipulate documents, and extend InDesign's functionality using JavaScript (or AppleScript/VBScript).

## Setting Up Your Environment

### 1. Script Folder Location

**macOS:**
```
~/Library/Preferences/Adobe InDesign/Version XX.0/en_US/Scripts/Scripts Panel/
```

**Windows:**
```
C:\Users\[Username]\AppData\Roaming\Adobe\InDesign\Version XX.0\en_US\Scripts\Scripts Panel\
```

### 2. Create Your First Script

1. Open your text editor (VS Code, Sublime, or even TextEdit)
2. Create a new file: `hello_world.jsx`
3. Add this code:

```javascript
// hello_world.jsx
alert("Hello, InDesign World!");
```

4. Save the file to the Scripts Panel folder
5. In InDesign, go to **Window > Utilities > Scripts**
6. Double-click your script to run it

## Your First Useful Script

Let's create something practical - a script that shows document info:

```javascript
// document_info.jsx

// Check if a document is open
if (app.documents.length == 0) {
    alert("Please open a document first!");
} else {
    var doc = app.activeDocument;
    var info = "Document Information\n\n";
    info += "Name: " + doc.name + "\n";
    info += "Pages: " + doc.pages.length + "\n";
    info += "Layers: " + doc.layers.length + "\n";
    info += "Colors: " + doc.colors.length + "\n";
    
    alert(info);
}
```

## Understanding the Code

```javascript
app.documents.length == 0
```
- `app` = The InDesign application object
- `documents` = Collection of all open documents
- `length` = Number of items in collection

```javascript
var doc = app.activeDocument;
```
- Gets the currently active document
- Stores it in a variable for easy access

```javascript
doc.pages.length
```
- Accesses the pages collection
- Gets the count of pages

## Basic Script Structure

Every InDesign script typically follows this pattern:

```javascript
// 1. Check preconditions
if (app.documents.length == 0) {
    alert("Error: Need an open document!");
    exit();
}

// 2. Get the document
var doc = app.activeDocument;

// 3. Do your work
try {
    // Your code here
    alert("Success!");
} catch (e) {
    // Handle errors
    alert("Error: " + e.message);
}
```

## Running Scripts

### Method 1: Scripts Panel
- Window > Utilities > Scripts
- Double-click script

### Method 2: Keyboard Shortcut
- Assign shortcuts in Edit > Keyboard Shortcuts

### Method 3: ExtendScript Toolkit (Deprecated)
- Use for debugging in older versions

### Method 4: VS Code + ExtendScript Debugger
- Modern debugging approach (recommended)

## Quick Exercise

Create a script that:
1. Checks if a document is open
2. Shows the document name
3. Shows the number of text frames
4. Shows the number of image frames

**Solution:**
```javascript
// frame_counter.jsx

if (app.documents.length == 0) {
    alert("Please open a document!");
} else {
    var doc = app.activeDocument;
    var textFrames = doc.textFrames.length;
    var rectangles = doc.rectangles.length;
    var ovals = doc.ovals.length;
    
    var message = "Frame Count for: " + doc.name + "\n\n";
    message += "Text Frames: " + textFrames + "\n";
    message += "Rectangles: " + rectangles + "\n";
    message += "Ovals: " + ovals + "\n";
    message += "Total: " + (textFrames + rectangles + ovals);
    
    alert(message);
}
```

## Common Gotchas

### 1. File Extension
✅ Use `.jsx` (JavaScript for Adobe)  
❌ Not `.js`

### 2. Checking for Documents
Always check if documents are open before accessing them:
```javascript
if (app.documents.length > 0) {
    // Safe to proceed
}
```

### 3. Error Handling
Use try-catch blocks:
```javascript
try {
    // risky code
} catch (e) {
    alert("Error: " + e);
}
```

### 4. Comments
Use comments liberally:
```javascript
// Single line comment
/* Multi-line
   comment */
```

## Next Steps

Now that you have your environment set up and understand the basics, move on to [JavaScript Basics for InDesign](02-javascript-basics.md) to learn the JavaScript concepts you need.

## Key Takeaways

✓ Scripts go in the Scripts Panel folder  
✓ Use `.jsx` file extension  
✓ Always check if documents are open  
✓ Use `app.activeDocument` to get current document  
✓ Test scripts on backup documents first  
✓ Use `alert()` for simple output  
