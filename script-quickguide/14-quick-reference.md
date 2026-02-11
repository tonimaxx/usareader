# Lesson 14: Quick Reference Cheat Sheet

Fast reference for common InDesign scripting tasks.

## Document Operations

```javascript
// Check if document open
if (app.documents.length > 0) { }

// Get active document
var doc = app.activeDocument;

// Create new document
var doc = app.documents.add();

// Open document
var doc = app.open(File("/path/to/file.indd"));

// Save document
doc.save();
doc.save(File("/path/to/new.indd"));

// Close document
doc.close(SaveOptions.YES);
doc.close(SaveOptions.NO);

// Export PDF
doc.exportFile(
    ExportFormat.PDF_TYPE,
    File("~/Desktop/export.pdf"),
    false,
    app.pdfExportPresets.item("[High Quality Print]")
);
```

## Pages

```javascript
// Get pages
var firstPage = doc.pages[0];
var lastPage = doc.pages[doc.pages.length - 1];
var pageByName = doc.pages.item("1");

// Add page
doc.pages.add();
doc.pages.add(LocationOptions.AT_BEGINNING);
doc.pages.add(LocationOptions.AFTER, doc.pages[2]);

// Remove page
doc.pages[5].remove();

// Page properties
page.name;
page.appliedMaster;
page.side;

// Loop pages
for (var i = 0; i < doc.pages.length; i++) {
    var page = doc.pages[i];
}
```

## Text Frames

```javascript
// Create text frame
var frame = page.textFrames.add();
frame.geometricBounds = [y1, x1, y2, x2];
frame.contents = "Hello, World!";

// Get text
var text = frame.contents;

// Set properties
frame.parentStory.appliedFont = "Arial";
frame.parentStory.pointSize = 12;

// Paragraph alignment
frame.paragraphs[0].justification = Justification.LEFT_ALIGN;

// Link frames
frame.nextTextFrame = anotherFrame;

// Check overflow
if (frame.overflows) {
    // Has overflow text
}
```

## Rectangles and Shapes

```javascript
// Rectangle
var rect = page.rectangles.add();
rect.geometricBounds = [y1, x1, y2, x2];

// Oval
var oval = page.ovals.add();
oval.geometricBounds = [y1, x1, y2, x2];

// Fill and stroke
rect.fillColor = doc.colors.item("Red");
rect.strokeColor = doc.colors.item("Black");
rect.strokeWeight = 2;

// No fill/stroke
rect.fillColor = doc.colors.item("None");
rect.strokeColor = doc.colors.item("None");
```

## Geometric Bounds

```javascript
// [top, left, bottom, right] from page origin
// Using points (1/72 inch)

frame.geometricBounds = [72, 72, 144, 216];
// 1" from top, 1" from left, 2" high, 3" wide

// With units
frame.geometricBounds = ["1in", "1in", "3in", "4in"];

// Get dimensions
var bounds = frame.geometricBounds;
var width = bounds[3] - bounds[1];
var height = bounds[2] - bounds[0];
```

## Layers

```javascript
// Create layer
var layer = doc.layers.add();
layer.name = "New Layer";

// Layer properties
layer.visible = true;
layer.locked = false;
layer.layerColor = UIColors.BLUE;

// Set active
doc.activeLayer = layer;

// Move item to layer
item.itemLayer = layer;
```

## Colors

```javascript
// Create RGB color
var color = doc.colors.add();
color.name = "My Color";
color.model = ColorModel.PROCESS;
color.space = ColorSpace.RGB;
color.colorValue = [255, 0, 0]; // Red

// Create CMYK color
var color = doc.colors.add();
color.name = "Cyan";
color.model = ColorModel.PROCESS;
color.space = ColorSpace.CMYK;
color.colorValue = [100, 0, 0, 0];

// Get existing color
var red = doc.colors.item("Red");

// Apply color
frame.fillColor = red;
frame.strokeColor = red;

// Built-in colors
doc.colors.item("Black");
doc.colors.item("None");
doc.colors.item("[Paper]");
doc.colors.item("[Registration]");
```

## Paragraph Styles

```javascript
// Create style
var style = doc.paragraphStyles.add();
style.name = "Body Text";

// Set font
style.appliedFont = "Arial";
style.pointSize = 12;
style.leading = 14;

// Alignment
style.justification = Justification.LEFT_ALIGN;

// Spacing
style.spaceBefore = 0;
style.spaceAfter = 6;

// Apply to text
paragraph.appliedParagraphStyle = style;

// Get existing style
var heading = doc.paragraphStyles.item("Heading 1");
```

## Character Styles

```javascript
// Create style
var style = doc.characterStyles.add();
style.name = "Bold";

// Properties
style.appliedFont = "Arial";
style.fontStyle = "Bold";
style.pointSize = 12;
style.fillColor = doc.colors.item("Red");

// Apply to text
textRange.appliedCharacterStyle = style;
```

## Find and Replace

```javascript
// Setup
app.findTextPreferences = NothingEnum.NOTHING;
app.changeTextPreferences = NothingEnum.NOTHING;

// Find text
app.findTextPreferences.findWhat = "hello";
var found = doc.findText();

// Replace text
app.findTextPreferences.findWhat = "old";
app.changeTextPreferences.changeTo = "new";
doc.changeText();

// Clear preferences
app.findTextPreferences = NothingEnum.NOTHING;
app.changeTextPreferences = NothingEnum.NOTHING;
```

## GREP Find/Replace

```javascript
// Setup
app.findGrepPreferences = NothingEnum.NOTHING;
app.changeGrepPreferences = NothingEnum.NOTHING;

// Find pattern
app.findGrepPreferences.findWhat = "\\d{3}-\\d{4}";
var found = doc.findGrep();

// Replace with pattern
app.findGrepPreferences.findWhat = "(\\d{3})-(\\d{4})";
app.changeGrepPreferences.changeTo = "($1) $2";
doc.changeGrep();

// Clear
app.findGrepPreferences = NothingEnum.NOTHING;
app.changeGrepPreferences = NothingEnum.NOTHING;
```

## Selection

```javascript
// Get selection
var sel = app.selection;

// Check selection
if (app.selection.length > 0) {
    var item = app.selection[0];
}

// Select item
app.select(frame);

// Select multiple
app.select([frame1, frame2]);

// Clear selection
app.select(null);
```

## Images

```javascript
// Place image
var image = page.place(File("/path/to/image.jpg"))[0];

// Set position
image.geometricBounds = [y1, x1, y2, x2];

// Fit options
image.fit(FitOptions.FRAME_TO_CONTENT);
image.fit(FitOptions.CONTENT_TO_FRAME);
image.fit(FitOptions.PROPORTIONALLY);
image.fit(FitOptions.FILL_PROPORTIONALLY);

// Scale image content
image.horizontalScale = 50;
image.verticalScale = 50;

// Get placed file
var imagePath = image.itemLink.filePath;
```

## Measurements

```javascript
// Point units (1/72 inch)
var x = 72; // 1 inch

// Convert units
var inches = "2.5in";
var points = UnitValue(inches).as("pt"); // 180

// Common conversions
72 points = 1 inch
1 point = 0.352778 mm
1 inch = 25.4 mm
```

## User Input

```javascript
// Simple prompt
var input = prompt("Enter value:", "default");
if (!input) exit(); // User canceled

// Confirm dialog
var confirm = window.confirm("Continue?");
if (!confirm) exit();

// Alert
alert("Done!");

// File selection
var file = File.openDialog("Select file");
if (!file) exit();

// Folder selection
var folder = Folder.selectDialog("Select folder");
if (!folder) exit();
```

## Loops

```javascript
// For loop
for (var i = 0; i < count; i++) { }

// While loop
while (condition) { }

// Loop pages
for (var i = 0; i < doc.pages.length; i++) {
    var page = doc.pages[i];
}

// Loop text frames
for (var i = 0; i < page.textFrames.length; i++) {
    var frame = page.textFrames[i];
}
```

## Conditionals

```javascript
// If
if (condition) { }

// If-else
if (condition) { } else { }

// If-else-if
if (cond1) { }
else if (cond2) { }
else { }

// Ternary
var result = condition ? value1 : value2;

// Switch
switch (value) {
    case "a":
        break;
    case "b":
        break;
    default:
        break;
}
```

## Functions

```javascript
// Function declaration
function myFunction(param1, param2) {
    return param1 + param2;
}

// Call function
var result = myFunction(1, 2);

// Anonymous function
var func = function() {
    return "hello";
};
```

## Arrays

```javascript
// Create array
var arr = [1, 2, 3];

// Access
var first = arr[0];

// Length
var len = arr.length;

// Add to end
arr.push(4);

// Remove from end
var last = arr.pop();

// Loop
for (var i = 0; i < arr.length; i++) {
    var item = arr[i];
}
```

## Objects

```javascript
// Create object
var obj = {
    name: "John",
    age: 30
};

// Access property
var name = obj.name;
var age = obj["age"];

// Add property
obj.email = "john@example.com";

// Loop properties
for (var key in obj) {
    var value = obj[key];
}
```

## Strings

```javascript
// Concatenate
var full = "Hello" + " " + "World";

// Length
var len = str.length;

// Methods
str.toUpperCase();
str.toLowerCase();
str.indexOf("sub");
str.substring(0, 5);
str.replace("old", "new");
str.split(",");
str.trim();
```

## Numbers

```javascript
// Parse
var num = parseInt("42");
var float = parseFloat("3.14");

// Convert to string
var str = num.toString();

// Check if number
if (!isNaN(num)) { }

// Math
Math.round(3.5);  // 4
Math.floor(3.9);  // 3
Math.ceil(3.1);   // 4
Math.abs(-5);     // 5
Math.max(1, 5);   // 5
Math.min(1, 5);   // 1
Math.random();    // 0-1
```

## Error Handling

```javascript
// Try-catch
try {
    // risky code
} catch (e) {
    alert("Error: " + e.message);
}

// Finally
try {
    // code
} catch (e) {
    // handle
} finally {
    // always runs
}

// Throw error
throw new Error("Something went wrong");
```

## Performance

```javascript
// Disable redraw
app.scriptPreferences.enableRedraw = false;
// ... do work ...
app.scriptPreferences.enableRedraw = true;

// Undo group
app.doScript(
    function() {
        // operations
    },
    ScriptLanguage.JAVASCRIPT,
    [],
    UndoModes.ENTIRE_SCRIPT,
    "Script Name"
);

// Cache values
var count = doc.pages.length; // Don't recalculate
for (var i = 0; i < count; i++) { }
```

## Common Enumerations

```javascript
// Location
LocationOptions.AFTER
LocationOptions.BEFORE
LocationOptions.AT_BEGINNING
LocationOptions.AT_END

// Save
SaveOptions.YES
SaveOptions.NO
SaveOptions.ASK

// Alignment
HorizontalAlignment.LEFT_ALIGN
HorizontalAlignment.CENTER_ALIGN
HorizontalAlignment.RIGHT_ALIGN
Justification.LEFT_ALIGN
Justification.CENTER_ALIGN
Justification.RIGHT_ALIGN
Justification.FULLY_JUSTIFIED

// Export
ExportFormat.PDF_TYPE
ExportFormat.JPG
ExportFormat.PNG_FORMAT
ExportFormat.EPUB

// Fit
FitOptions.FRAME_TO_CONTENT
FitOptions.CONTENT_TO_FRAME
FitOptions.PROPORTIONALLY
FitOptions.FILL_PROPORTIONALLY

// Color
ColorSpace.RGB
ColorSpace.CMYK
ColorModel.PROCESS
ColorModel.SPOT
```

## File Operations

```javascript
// File object
var file = File("/path/to/file.txt");

// Check exists
if (file.exists) { }

// Read file
file.open("r");
var content = file.read();
file.close();

// Write file
file.open("w");
file.write("content");
file.close();

// File dialog
var file = File.openDialog("Select file");
var file = File.saveDialog("Save as");

// Folder
var folder = Folder("/path/to/folder");
if (folder.exists) { }
var files = folder.getFiles("*.jpg");
```

## Special Characters

```javascript
SpecialCharacters.AUTO_PAGE_NUMBER
SpecialCharacters.SECTION_MARKER
SpecialCharacters.EM_DASH
SpecialCharacters.EN_DASH
SpecialCharacters.BULLET_CHARACTER
SpecialCharacters.COPYRIGHT_SYMBOL
SpecialCharacters.REGISTERED_TRADEMARK
SpecialCharacters.TRADEMARK_SYMBOL
SpecialCharacters.PARAGRAPH_SYMBOL
```

## Debugging

```javascript
// Console output
$.writeln("Debug: " + variable);

// Alert
alert("Value: " + value);

// Error line
try {
    // code
} catch (e) {
    alert("Error at line " + e.line + ": " + e.message);
}

// Script file path
$.fileName;

// Break into debugger
$.bp();
```

## Common Mistakes

```javascript
// ✗ Wrong - comparing object
if (doc.activeLayer == layer) { }

// ✓ Right - comparing properties
if (doc.activeLayer.id == layer.id) { }

// ✗ Wrong - collection doesn't exist
if (!doc.pages) { }

// ✓ Right - check length
if (doc.pages.length > 0) { }

// ✗ Wrong - item() returns object even if doesn't exist
if (doc.colors.item("Red")) { }

// ✓ Right - try-catch
try {
    var red = doc.colors.item("Red");
    red.name; // Will error if doesn't exist
} catch (e) {
    // Doesn't exist
}
```

## Resources

- [Adobe InDesign Scripting Guide](https://www.adobe.com/devnet/indesign/sdk.html)
- [InDesign Scripting Forum](https://community.adobe.com/t5/indesign/ct-p/ct-indesign)
- [InDesign Secrets](https://indesignsecrets.com/topic/scripting)
- [JavaScript Tools Guide](https://extendscript.docsforadobe.dev/)

---

**Print this page for quick reference while scripting!**
