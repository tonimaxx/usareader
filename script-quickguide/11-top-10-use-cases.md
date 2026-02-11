# Lesson 11: Top 10 Use Cases with Complete Code

Real-world InDesign scripting examples you can use immediately.

---

## Use Case 1: Batch Export Pages to PDF

**Problem:** Export specific pages individually to separate PDF files  
**Use:** Creating page proofs, client reviews, individual page assets

```javascript
// batch_export_pages.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;
var exportPreset = app.pdfExportPresets.item("[High Quality Print]");
var exportFolder = Folder.selectDialog("Choose export folder");

if (exportFolder != null) {
    // Prompt for pages
    var pagesInput = prompt("Enter page numbers (comma-separated):", "1,2,3");
    
    if (pagesInput) {
        var pagesArray = pagesInput.split(",");
        var pagesToExport = [];
        
        for (var j = 0; j < pagesArray.length; j++) {
            var pageNum = parseInt(pagesArray[j].replace(/\s/g, ""), 10);
            if (!isNaN(pageNum)) {
                pagesToExport.push(pageNum);
            }
        }
        
        // Export each page
        for (var i = 0; i < pagesToExport.length; i++) {
            var pageNum = pagesToExport[i];
            
            try {
                var page = doc.pages.itemByName(pageNum.toString());
                var pdfFile = File(exportFolder + "/Page_" + pageNum + ".pdf");
                
                app.pdfExportPreferences.pageRange = page.name;
                doc.exportFile(ExportFormat.PDF_TYPE, pdfFile, false, exportPreset);
                
            } catch (e) {
                alert("Failed to export page " + pageNum + ": " + e.message);
            }
        }
        
        alert("Export complete! " + pagesToExport.length + " pages exported.");
    }
} else {
    alert("Export canceled.");
}
```

---

## Use Case 2: Find and Replace Text Across Document

**Problem:** Update text content throughout document  
**Use:** Client name changes, version updates, corrections

```javascript
// find_replace_text.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Get search terms from user
var findWhat = prompt("Find what:", "");
if (!findWhat) {
    alert("Search canceled.");
    exit();
}

var replaceWith = prompt("Replace with:", "");
if (replaceWith == null) {
    alert("Replace canceled.");
    exit();
}

// Setup find/change preferences
app.findTextPreferences = NothingEnum.NOTHING;
app.changeTextPreferences = NothingEnum.NOTHING;

app.findTextPreferences.findWhat = findWhat;
app.changeTextPreferences.changeTo = replaceWith;

// Perform find/replace
var foundItems = doc.findText();
var count = foundItems.length;

if (count > 0) {
    var confirm = window.confirm(
        "Found " + count + " occurrence(s).\n" +
        "Replace all?"
    );
    
    if (confirm) {
        doc.changeText();
        alert("Replaced " + count + " occurrence(s).");
    }
} else {
    alert("No matches found.");
}

// Clear preferences
app.findTextPreferences = NothingEnum.NOTHING;
app.changeTextPreferences = NothingEnum.NOTHING;
```

---

## Use Case 3: Apply Paragraph Style to All Headings

**Problem:** Consistently format all heading text  
**Use:** Style cleanup, template application, formatting automation

```javascript
// apply_heading_styles.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Check if style exists
var styleName = "Heading 1";
var style;

try {
    style = doc.paragraphStyles.item(styleName);
    style.name; // Test if exists
} catch (e) {
    alert("Style '" + styleName + "' not found!\nPlease create it first.");
    exit();
}

// Setup find preferences for heading patterns
app.findGrepPreferences = NothingEnum.NOTHING;
app.changeGrepPreferences = NothingEnum.NOTHING;

// Find paragraphs starting with "Chapter" or numbers
app.findGrepPreferences.findWhat = "^(Chapter|\\d+\\.).*$";

// Apply style
app.changeGrepPreferences.appliedParagraphStyle = style;

var foundItems = doc.findGrep();
var count = foundItems.length;

if (count > 0) {
    doc.changeGrep();
    alert("Applied '" + styleName + "' to " + count + " paragraph(s).");
} else {
    alert("No matching paragraphs found.");
}

// Clear preferences
app.findGrepPreferences = NothingEnum.NOTHING;
app.changeGrepPreferences = NothingEnum.NOTHING;
```

---

## Use Case 4: Create Table of Contents from Styles

**Problem:** Generate TOC automatically  
**Use:** Books, reports, documentation

```javascript
// create_toc.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Styles to include in TOC
var tocStyles = ["Heading 1", "Heading 2"];
var tocData = [];

// Loop through all text frames
for (var i = 0; i < doc.textFrames.length; i++) {
    var frame = doc.textFrames[i];
    
    // Loop through paragraphs in frame
    for (var j = 0; j < frame.paragraphs.length; j++) {
        var para = frame.paragraphs[j];
        var styleName = para.appliedParagraphStyle.name;
        
        // Check if paragraph uses a TOC style
        for (var k = 0; k < tocStyles.length; k++) {
            if (styleName == tocStyles[k]) {
                var text = para.contents.replace(/\r$/, ""); // Remove line break
                var pageNum = para.parentTextFrames[0].parentPage.name;
                
                tocData.push({
                    text: text,
                    page: pageNum,
                    level: k + 1
                });
                break;
            }
        }
    }
}

// Build TOC text
if (tocData.length > 0) {
    var tocText = "TABLE OF CONTENTS\n\n";
    
    for (var i = 0; i < tocData.length; i++) {
        var item = tocData[i];
        var indent = new Array(item.level).join("  "); // Indent by level
        tocText += indent + item.text + " ... " + item.page + "\n";
    }
    
    // Create new page for TOC at beginning
    var tocPage = doc.pages.add(LocationOptions.AT_BEGINNING);
    
    // Create text frame for TOC
    var marginPrefs = doc.marginPreferences;
    var bounds = [
        marginPrefs.top,
        marginPrefs.left,
        doc.documentPreferences.pageHeight - marginPrefs.bottom,
        doc.documentPreferences.pageWidth - marginPrefs.right
    ];
    
    var tocFrame = tocPage.textFrames.add();
    tocFrame.geometricBounds = bounds;
    tocFrame.contents = tocText;
    
    alert("TOC created with " + tocData.length + " entries on page 1.");
} else {
    alert("No TOC entries found.\nMake sure you have paragraphs styled with: " + tocStyles.join(", "));
}
```

---

## Use Case 5: Resize All Images Proportionally

**Problem:** Standardize image sizes  
**Use:** Template compliance, layout optimization, file size reduction

```javascript
// resize_all_images.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Get target scale percentage
var scaleInput = prompt("Enter scale percentage (e.g., 50 for 50%):", "100");
if (!scaleInput) {
    alert("Canceled.");
    exit();
}

var scalePercent = parseFloat(scaleInput);
if (isNaN(scalePercent) || scalePercent <= 0) {
    alert("Invalid scale value!");
    exit();
}

var scaleFactor = scalePercent / 100;

// Find all rectangles with images
var count = 0;

for (var i = 0; i < doc.allPageItems.length; i++) {
    var item = doc.allPageItems[i];
    
    // Check if item is a frame with an image
    if (item.images.length > 0) {
        try {
            var image = item.images[0];
            
            // Scale the image
            image.horizontalScale = scalePercent;
            image.verticalScale = scalePercent;
            
            // Optionally fit frame to scaled image
            item.fit(FitOptions.FRAME_TO_CONTENT);
            
            count++;
        } catch (e) {
            // Skip if error
        }
    }
}

alert("Scaled " + count + " image(s) to " + scalePercent + "%");
```

---

## Use Case 6: Add Page Numbers to All Pages

**Problem:** Automatically number pages  
**Use:** Documentation, books, reports

```javascript
// add_page_numbers.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Get position preference
var positions = ["Bottom Center", "Bottom Right", "Bottom Left", "Top Center"];
var positionChoice = prompt(
    "Choose position:\n" +
    "1 = Bottom Center\n" +
    "2 = Bottom Right\n" +
    "3 = Bottom Left\n" +
    "4 = Top Center",
    "1"
);

if (!positionChoice) {
    alert("Canceled.");
    exit();
}

var position = parseInt(positionChoice) - 1;
if (position < 0 || position > 3) {
    alert("Invalid choice!");
    exit();
}

// Add page numbers to each page
for (var i = 0; i < doc.pages.length; i++) {
    var page = doc.pages[i];
    var pageWidth = doc.documentPreferences.pageWidth;
    var pageHeight = doc.documentPreferences.pageHeight;
    var margin = 36; // 0.5 inch
    
    var frameWidth = 72; // 1 inch
    var frameHeight = 18; // 0.25 inch
    
    var bounds;
    
    // Calculate position
    switch (position) {
        case 0: // Bottom Center
            bounds = [
                pageHeight - margin - frameHeight,
                (pageWidth - frameWidth) / 2,
                pageHeight - margin,
                (pageWidth + frameWidth) / 2
            ];
            break;
        case 1: // Bottom Right
            bounds = [
                pageHeight - margin - frameHeight,
                pageWidth - margin - frameWidth,
                pageHeight - margin,
                pageWidth - margin
            ];
            break;
        case 2: // Bottom Left
            bounds = [
                pageHeight - margin - frameHeight,
                margin,
                pageHeight - margin,
                margin + frameWidth
            ];
            break;
        case 3: // Top Center
            bounds = [
                margin,
                (pageWidth - frameWidth) / 2,
                margin + frameHeight,
                (pageWidth + frameWidth) / 2
            ];
            break;
    }
    
    // Create text frame
    var pageNumFrame = page.textFrames.add();
    pageNumFrame.geometricBounds = bounds;
    pageNumFrame.contents = SpecialCharacters.AUTO_PAGE_NUMBER;
    
    // Center align
    pageNumFrame.paragraphs[0].justification = Justification.CENTER_ALIGN;
}

alert("Added page numbers to " + doc.pages.length + " pages at " + positions[position]);
```

---

## Use Case 7: Create Color Swatches from List

**Problem:** Add multiple colors at once  
**Use:** Brand guidelines, template setup, color management

```javascript
// create_color_swatches.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Define colors (RGB values)
var colors = [
    { name: "Brand Red", rgb: [230, 0, 0] },
    { name: "Brand Blue", rgb: [0, 102, 204] },
    { name: "Brand Green", rgb: [0, 153, 76] },
    { name: "Brand Orange", rgb: [255, 128, 0] },
    { name: "Brand Purple", rgb: [153, 51, 204] },
    { name: "Light Gray", rgb: [220, 220, 220] },
    { name: "Dark Gray", rgb: [64, 64, 64] }
];

var created = 0;
var skipped = 0;

// Create each color
for (var i = 0; i < colors.length; i++) {
    var colorData = colors[i];
    
    try {
        // Check if color already exists
        var existingColor = doc.colors.item(colorData.name);
        existingColor.name; // Will throw error if doesn't exist
        skipped++;
    } catch (e) {
        // Color doesn't exist, create it
        var newColor = doc.colors.add();
        newColor.name = colorData.name;
        newColor.model = ColorModel.PROCESS;
        newColor.space = ColorSpace.RGB;
        newColor.colorValue = colorData.rgb;
        created++;
    }
}

alert(
    "Color Swatches:\n" +
    "Created: " + created + "\n" +
    "Skipped (already exist): " + skipped
);
```

---

## Use Case 8: Link All Overset Text Frames

**Problem:** Connect text frames that have overflow  
**Use:** Long documents, automatic text flow, layout management

```javascript
// link_overset_frames.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;

// Find all overset text frames
var oversetFrames = [];

for (var i = 0; i < doc.textFrames.length; i++) {
    var frame = doc.textFrames[i];
    
    // Check if frame has overset text and is not already linked
    if (frame.overflows && frame.nextTextFrame == null) {
        oversetFrames.push(frame);
    }
}

if (oversetFrames.length == 0) {
    alert("No overset text frames found!");
    exit();
}

// For each overset frame, create a new frame on next page
var linked = 0;

for (var i = 0; i < oversetFrames.length; i++) {
    var frame = oversetFrames[i];
    var currentPage = frame.parentPage;
    
    // Find or create next page
    var nextPage;
    var pageIndex = doc.pages.indexOf(currentPage);
    
    if (pageIndex < doc.pages.length - 1) {
        nextPage = doc.pages[pageIndex + 1];
    } else {
        nextPage = doc.pages.add();
    }
    
    // Create new text frame on next page with same bounds
    var newFrame = nextPage.textFrames.add();
    newFrame.geometricBounds = frame.geometricBounds;
    
    // Link frames
    frame.nextTextFrame = newFrame;
    linked++;
}

alert("Linked " + linked + " overset text frame(s).");
```

---

## Use Case 9: Export Each Layer as Separate PDF

**Problem:** Create versions showing different content  
**Use:** Multilingual documents, design iterations, client options

```javascript
// export_layers_separately.jsx

if (app.documents.length == 0) {
    alert("Please open a document first!");
    exit();
}

var doc = app.activeDocument;
var exportFolder = Folder.selectDialog("Choose export folder");

if (!exportFolder) {
    alert("Canceled.");
    exit();
}

var exportPreset = app.pdfExportPresets.item("[High Quality Print]");

// Store original layer visibility
var layerStates = [];
for (var i = 0; i < doc.layers.length; i++) {
    layerStates.push({
        layer: doc.layers[i],
        visible: doc.layers[i].visible
    });
}

// Export each layer
for (var i = 0; i < doc.layers.length; i++) {
    var layer = doc.layers[i];
    
    // Hide all layers
    for (var j = 0; j < doc.layers.length; j++) {
        doc.layers[j].visible = false;
    }
    
    // Show only current layer
    layer.visible = true;
    
    // Export PDF
    var filename = doc.name.replace(/\.indd$/, "") + "_" + layer.name + ".pdf";
    var pdfFile = File(exportFolder + "/" + filename);
    
    try {
        doc.exportFile(ExportFormat.PDF_TYPE, pdfFile, false, exportPreset);
    } catch (e) {
        alert("Failed to export layer '" + layer.name + "': " + e.message);
    }
}

// Restore original layer visibility
for (var i = 0; i < layerStates.length; i++) {
    layerStates[i].layer.visible = layerStates[i].visible;
}

alert("Exported " + doc.layers.length + " layer(s) as separate PDFs.");
```

---

## Use Case 10: Generate Data-Driven Document from CSV

**Problem:** Create personalized documents from data  
**Use:** Certificates, business cards, name tags, mail merge

```javascript
// data_merge_from_csv.jsx

if (app.documents.length == 0) {
    alert("Please open a template document first!");
    exit();
}

var doc = app.activeDocument;

// Select CSV file
var csvFile = File.openDialog("Select CSV file");
if (!csvFile) {
    alert("Canceled.");
    exit();
}

// Read CSV file
csvFile.open("r");
var csvContent = csvFile.read();
csvFile.close();

// Parse CSV (simple parser)
var lines = csvContent.split("\n");
var headers = lines[0].split(",");
var records = [];

for (var i = 1; i < lines.length; i++) {
    if (lines[i].trim() == "") continue;
    
    var values = lines[i].split(",");
    var record = {};
    
    for (var j = 0; j < headers.length; j++) {
        record[headers[j].trim()] = values[j] ? values[j].trim() : "";
    }
    
    records.push(record);
}

if (records.length == 0) {
    alert("No data found in CSV!");
    exit();
}

// Show available fields
var fieldList = "";
for (var i = 0; i < headers.length; i++) {
    fieldList += headers[i].trim() + "\n";
}

alert("Found " + records.length + " record(s) with fields:\n\n" + fieldList);

// Create a document for each record
var templatePage = doc.pages[0];
var created = 0;

for (var i = 0; i < records.length; i++) {
    var record = records[i];
    
    // Duplicate the template page
    var newPage;
    if (i == 0) {
        newPage = templatePage;
    } else {
        newPage = doc.pages.add();
        
        // Copy all items from template
        for (var j = 0; j < templatePage.allPageItems.length; j++) {
            templatePage.allPageItems[j].duplicate(newPage);
        }
    }
    
    // Replace placeholders in text frames
    for (var j = 0; j < newPage.textFrames.length; j++) {
        var frame = newPage.textFrames[j];
        var text = frame.contents;
        
        // Replace each field
        for (var key in record) {
            var placeholder = "{{" + key + "}}";
            text = text.replace(new RegExp(placeholder, "g"), record[key]);
        }
        
        frame.contents = text;
    }
    
    created++;
}

alert("Created " + created + " page(s) from data.");
```

**CSV Example:**
```csv
Name,Title,Company,Email
John Doe,Manager,Acme Corp,john@acme.com
Jane Smith,Director,Tech Inc,jane@tech.com
```

**Template Usage:**
Create text frames with placeholders:
- `{{Name}}`
- `{{Title}}`
- `{{Company}}`
- `{{Email}}`

---

## Bonus: Script Runner Menu

Create a master script that runs other scripts:

```javascript
// script_runner.jsx

var scripts = [
    { name: "Batch Export Pages", file: "batch_export_pages.jsx" },
    { name: "Find & Replace", file: "find_replace_text.jsx" },
    { name: "Add Page Numbers", file: "add_page_numbers.jsx" },
    { name: "Create Color Swatches", file: "create_color_swatches.jsx" },
    { name: "Resize All Images", file: "resize_all_images.jsx" }
];

// Build menu
var menu = "Select a script to run:\n\n";
for (var i = 0; i < scripts.length; i++) {
    menu += (i + 1) + ". " + scripts[i].name + "\n";
}

var choice = prompt(menu, "1");
if (!choice) {
    exit();
}

var index = parseInt(choice) - 1;
if (index < 0 || index >= scripts.length) {
    alert("Invalid choice!");
    exit();
}

// Get script folder
var scriptFile = File($.fileName);
var scriptFolder = scriptFile.parent;

// Run selected script
var targetScript = File(scriptFolder + "/" + scripts[index].file);
if (targetScript.exists) {
    app.doScript(targetScript);
} else {
    alert("Script file not found:\n" + targetScript.fsName);
}
```

---

## Testing Your Scripts

Before using on production documents:

1. **Test on copies** - Always work on duplicates
2. **Use small samples** - Test with 2-3 pages first
3. **Check results** - Verify output manually
4. **Add error handling** - Use try-catch blocks
5. **Save versions** - Keep backups before running

## Next Steps

- Review [Best Practices](12-best-practices.md) for optimization
- Check [Debugging](13-debugging.md) for troubleshooting
- Consult [Quick Reference](14-quick-reference.md) for syntax

## Key Takeaways

✓ Always check if documents are open  
✓ Prompt users for input when needed  
✓ Use try-catch for error handling  
✓ Provide feedback via alerts  
✓ Test on copies before production  
✓ Clear find/change preferences after use  
✓ Save original state before modifications  
✓ Give users cancel options  
