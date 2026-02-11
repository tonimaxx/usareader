# Lesson 3: InDesign Object Model

Understanding InDesign's Document Object Model (DOM) is crucial for effective scripting.

## The Object Hierarchy

```
app (Application)
└── documents (Documents Collection)
    └── document (Document)
        ├── pages (Pages Collection)
        │   └── page (Page)
        │       ├── textFrames (Text Frames)
        │       ├── rectangles (Rectangles)
        │       ├── ovals (Ovals)
        │       └── groups (Groups)
        ├── layers (Layers Collection)
        │   └── layer (Layer)
        ├── paragraphStyles (Paragraph Styles)
        ├── characterStyles (Character Styles)
        ├── colors (Colors/Swatches)
        └── masterSpreads (Master Pages)
```

## The Application Object

The `app` object represents InDesign itself:

```javascript
// Application properties
app.name;                    // "Adobe InDesign"
app.version;                 // "16.0" (version number)
app.documents;               // Collection of open documents
app.activeDocument;          // Currently active document
app.activeWindow;            // Currently active window

// Application methods
app.open(File("path/to/file.indd"));
app.quit();

// Preferences
app.scriptPreferences.userInteractionLevel;
```

## Documents

```javascript
// Check if documents are open
if (app.documents.length > 0) {
    // Safe to proceed
}

// Get active document
var doc = app.activeDocument;

// Create new document
var newDoc = app.documents.add();

// Open existing document
var file = File("/path/to/document.indd");
if (file.exists) {
    var doc = app.open(file);
}

// Save document
doc.save();
doc.save(File("/path/to/new-name.indd"));

// Close document
doc.close(SaveOptions.YES);
doc.close(SaveOptions.NO);
doc.close(SaveOptions.ASK);

// Document properties
doc.name;                    // "MyDocument.indd"
doc.fullName;                // Full file path
doc.modified;                // true/false
doc.saved;                   // true/false
doc.documentPreferences;     // Page size, margins, etc.
```

## Document Preferences

```javascript
var doc = app.activeDocument;

// Page setup
with (doc.documentPreferences) {
    pageWidth = "8.5in";
    pageHeight = "11in";
    facingPages = true;
    pagesPerDocument = 10;
}

// Margins and columns
with (doc.marginPreferences) {
    top = "0.5in";
    bottom = "0.5in";
    left = "0.75in";
    right = "0.75in";
    columnCount = 2;
    columnGutter = "0.25in";
}
```

## Pages

```javascript
var doc = app.activeDocument;

// Access pages
var firstPage = doc.pages[0];
var lastPage = doc.pages[doc.pages.length - 1];
var pageByName = doc.pages.item("1");

// Page properties
firstPage.name;              // "1"
firstPage.appliedMaster;     // Master page applied
firstPage.side;              // PageSideOptions.LEFT_HAND

// Add pages
doc.pages.add();                                    // Add at end
doc.pages.add(LocationOptions.AT_END);              // Add at end
doc.pages.add(LocationOptions.AT_BEGINNING);        // Add at start
doc.pages.add(LocationOptions.AFTER, doc.pages[2]); // After page 3

// Remove pages
doc.pages[5].remove();
doc.pages.item(5).remove();

// Page count
var pageCount = doc.pages.length;

// Loop through pages
for (var i = 0; i < doc.pages.length; i++) {
    var page = doc.pages[i];
    alert("Page " + page.name);
}
```

## Layers

```javascript
var doc = app.activeDocument;

// Access layers
var firstLayer = doc.layers[0];
var layerByName = doc.layers.item("Text");

// Create layer
var newLayer = doc.layers.add();
newLayer.name = "Images";

// Layer properties
newLayer.visible = true;
newLayer.locked = false;
newLayer.layerColor = UIColors.BLUE;

// Set active layer
doc.activeLayer = newLayer;

// Remove layer
doc.layers.item("Old Layer").remove();
```

## Page Items (Frames, Shapes)

All visual objects on a page are "page items":

```javascript
var doc = app.activeDocument;
var page = doc.pages[0];

// Text frames
var textFrame = page.textFrames.add();
textFrame.geometricBounds = [10, 10, 100, 200]; // [y1, x1, y2, x2]
textFrame.contents = "Hello, World!";

// Rectangles
var rect = page.rectangles.add();
rect.geometricBounds = [50, 50, 150, 250];
rect.fillColor = doc.colors.item("Red");
rect.strokeColor = doc.colors.item("Black");
rect.strokeWeight = 2;

// Ovals
var oval = page.ovals.add();
oval.geometricBounds = [200, 50, 300, 150];

// Access all page items on a page
var allItems = page.allPageItems;
var textFrames = page.textFrames;
var rectangles = page.rectangles;
var ovals = page.ovals;
var groups = page.groups;
```

## Geometric Bounds

Geometric bounds define position and size: `[y1, x1, y2, x2]`

```javascript
// [top, left, bottom, right]
// All measurements from top-left of page

var frame = page.textFrames.add();
frame.geometricBounds = [
    "1in",    // top (y1)
    "1in",    // left (x1)
    "3in",    // bottom (y2)
    "5in"     // right (x2)
];

// Getting dimensions
var bounds = frame.geometricBounds;
var top = bounds[0];
var left = bounds[1];
var bottom = bounds[2];
var right = bounds[3];

var width = right - left;
var height = bottom - top;
```

## Collections

Collections in InDesign are array-like but have special methods:

```javascript
var pages = doc.pages;

// Access by index (zero-based)
var firstPage = pages[0];

// Access by item() method
var firstPageAlt = pages.item(0);
var namedPage = pages.item("1");

// Count
var count = pages.length;

// Check if empty
if (pages.length > 0) {
    // Has items
}

// Loop through collection
for (var i = 0; i < pages.length; i++) {
    var page = pages[i];
    // Do something
}

// everyItem() - apply to all items
doc.pages.everyItem().appliedMaster = doc.masterSpreads[0];

// Remove all items
doc.pages.everyItem().remove();  // Dangerous!
```

## Object References

```javascript
// Get parent of an object
var frame = doc.textFrames[0];
var parentPage = frame.parent;          // The page
var parentDoc = parentPage.parent;      // The document

// Get document from any object
var doc = frame.parent.parent;

// Or use a shortcut
var doc = frame.parentPage.parent;
```

## Selection

```javascript
// Get current selection
var selection = app.selection;

// Check if anything is selected
if (app.selection.length > 0) {
    var selected = app.selection[0];
    alert(selected.constructor.name);
}

// Select object
var frame = doc.textFrames[0];
app.select(frame);

// Select multiple objects
app.select([frame1, frame2, frame3]);

// Clear selection
app.select(null);
```

## Common Object Properties

Most InDesign objects share these properties:

```javascript
var frame = doc.textFrames[0];

// Identification
frame.id;                    // Unique ID
frame.name;                  // Object name
frame.label;                 // Script label

// Position and size
frame.geometricBounds;       // [y1, x1, y2, x2]
frame.visibleBounds;         // Including stroke

// Appearance
frame.fillColor;             // Color object
frame.strokeColor;           // Color object
frame.strokeWeight;          // Number
frame.opacity;               // 0-100

// Transform
frame.rotationAngle;         // Degrees
frame.horizontalScale;       // Percentage
frame.verticalScale;         // Percentage

// Visibility
frame.visible;               // true/false
frame.locked;                // true/false

// Layer
frame.itemLayer;             // Layer object
```

## Object Methods

```javascript
var frame = doc.textFrames[0];

// Duplication
var copy = frame.duplicate();
var copyToPage = frame.duplicate(doc.pages[2]);

// Movement
frame.move(doc.pages[2]);
frame.move([100, 100]);  // To specific location

// Removal
frame.remove();

// Transformation
frame.resize(
    CoordinateSpaces.INNER_COORDINATES,
    AnchorPoint.CENTER_ANCHOR,
    ResizeMethods.MULTIPLYING_CURRENT_DIMENSIONS_BY,
    [2.0, 2.0]  // Double size
);

// Export
frame.exportFile(
    ExportFormat.JPG,
    File("~/Desktop/frame.jpg")
);
```

## Constants and Enumerations

InDesign uses enumerations for many values:

```javascript
// Location options
LocationOptions.AFTER
LocationOptions.BEFORE
LocationOptions.AT_BEGINNING
LocationOptions.AT_END

// Save options
SaveOptions.YES
SaveOptions.NO
SaveOptions.ASK

// Page side
PageSideOptions.LEFT_HAND
PageSideOptions.RIGHT_HAND
PageSideOptions.SINGLE_SIDED

// Horizontal alignment
HorizontalAlignment.LEFT_ALIGN
HorizontalAlignment.CENTER_ALIGN
HorizontalAlignment.RIGHT_ALIGN

// Vertical alignment
VerticalAlignment.TOP_ALIGN
VerticalAlignment.CENTER_ALIGN
VerticalAlignment.BOTTOM_ALIGN

// Export formats
ExportFormat.PDF_TYPE
ExportFormat.JPG
ExportFormat.PNG_FORMAT
ExportFormat.EPUB

// Coordinate spaces
CoordinateSpaces.PASTEBOARD_COORDINATES
CoordinateSpaces.INNER_COORDINATES

// Units
MeasurementUnits.POINTS
MeasurementUnits.INCHES
MeasurementUnits.MILLIMETERS
```

## Practical Example: Document Report

```javascript
// document_report.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
} else {
    var doc = app.activeDocument;
    
    // Gather information
    var report = "=== DOCUMENT REPORT ===\n\n";
    
    // Basic info
    report += "Name: " + doc.name + "\n";
    report += "Saved: " + (doc.saved ? "Yes" : "No") + "\n";
    report += "Modified: " + (doc.modified ? "Yes" : "No") + "\n\n";
    
    // Structure
    report += "--- Structure ---\n";
    report += "Pages: " + doc.pages.length + "\n";
    report += "Layers: " + doc.layers.length + "\n";
    report += "Master Spreads: " + doc.masterSpreads.length + "\n\n";
    
    // Content
    report += "--- Content ---\n";
    report += "Text Frames: " + doc.textFrames.length + "\n";
    report += "Rectangles: " + doc.rectangles.length + "\n";
    report += "Ovals: " + doc.ovals.length + "\n\n";
    
    // Styles
    report += "--- Styles ---\n";
    report += "Paragraph Styles: " + doc.paragraphStyles.length + "\n";
    report += "Character Styles: " + doc.characterStyles.length + "\n";
    report += "Colors: " + doc.colors.length + "\n\n";
    
    // Page size
    var pageWidth = doc.documentPreferences.pageWidth;
    var pageHeight = doc.documentPreferences.pageHeight;
    report += "--- Page Setup ---\n";
    report += "Size: " + pageWidth + " x " + pageHeight + "\n";
    report += "Facing Pages: " + doc.documentPreferences.facingPages + "\n";
    
    alert(report);
}
```

## Next Steps

Now that you understand the object model, proceed to:
- [Working with Documents](04-documents.md) for document operations
- [Top 10 Use Cases](11-top-10-use-cases.md) for practical examples

## Key Takeaways

✓ `app` is the root object  
✓ Collections are array-like (use length and [index])  
✓ Use `item()` method for named access  
✓ Geometric bounds: [y1, x1, y2, x2]  
✓ Check `app.documents.length` before accessing docs  
✓ Most objects have parent, id, name properties  
✓ Use enumerations for options (SaveOptions.YES)  
✓ Selection is accessed via `app.selection`  
