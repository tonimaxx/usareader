# Lesson 2: JavaScript Basics for InDesign

InDesign uses ExtendScript, which is based on ECMAScript 3 (JavaScript from 1999). This means no modern features like arrow functions, let/const, or template literals.

## Variables

```javascript
// Declaring variables
var docName = "MyDocument";
var pageCount = 10;
var isPublished = false;

// Multiple declarations
var width = 100, height = 200;

// Variable naming
var myVariable;      // ✓ camelCase (recommended)
var my_variable;     // ✓ snake_case (ok)
var MyVariable;      // ✓ PascalCase (for constructors)
var my-variable;     // ✗ Invalid
```

## Data Types

```javascript
// String
var title = "My Document";
var subtitle = 'Chapter 1';  // Single or double quotes

// Number
var pages = 24;
var price = 19.99;
var negative = -5;

// Boolean
var isActive = true;
var isDraft = false;

// Array
var colors = ["Red", "Green", "Blue"];
var numbers = [1, 2, 3, 4, 5];

// Object
var book = {
    title: "InDesign Guide",
    pages: 200,
    author: "John Doe"
};

// Null and Undefined
var empty = null;
var notSet;  // undefined
```

## Operators

```javascript
// Arithmetic
var sum = 10 + 5;        // 15
var difference = 10 - 5; // 5
var product = 10 * 5;    // 50
var quotient = 10 / 5;   // 2
var remainder = 10 % 3;  // 1

// Comparison
10 == "10"   // true (loose equality)
10 === "10"  // false (strict equality)
10 != 5      // true
10 !== "10"  // true
10 > 5       // true
10 < 5       // false
10 >= 10     // true

// Logical
true && false  // false (AND)
true || false  // true (OR)
!true          // false (NOT)

// String concatenation
var fullName = "John" + " " + "Doe";  // "John Doe"
```

## Conditionals

```javascript
// If statement
if (pageCount > 100) {
    alert("Large document");
}

// If-else
if (pageCount > 100) {
    alert("Large document");
} else {
    alert("Small document");
}

// If-else-if
if (pageCount > 100) {
    alert("Large");
} else if (pageCount > 50) {
    alert("Medium");
} else {
    alert("Small");
}

// Ternary operator
var size = (pageCount > 100) ? "Large" : "Small";

// Switch statement
switch (fileType) {
    case "pdf":
        exportPDF();
        break;
    case "epub":
        exportEPUB();
        break;
    default:
        alert("Unknown type");
}
```

## Loops

```javascript
// For loop (most common)
for (var i = 0; i < 10; i++) {
    // runs 10 times
    alert(i);
}

// Looping through array
var colors = ["Red", "Green", "Blue"];
for (var i = 0; i < colors.length; i++) {
    alert(colors[i]);
}

// While loop
var i = 0;
while (i < 10) {
    alert(i);
    i++;
}

// Do-while loop
var i = 0;
do {
    alert(i);
    i++;
} while (i < 10);

// Breaking out of loops
for (var i = 0; i < 100; i++) {
    if (i == 10) {
        break;  // Exit loop
    }
}

// Skipping iterations
for (var i = 0; i < 10; i++) {
    if (i % 2 == 0) {
        continue;  // Skip even numbers
    }
    alert(i);  // Only odd numbers
}
```

## Functions

```javascript
// Function declaration
function greet(name) {
    return "Hello, " + name;
}

// Calling function
var message = greet("John");  // "Hello, John"

// Function with multiple parameters
function addPage(doc, position) {
    doc.pages.add(LocationOptions.AFTER, doc.pages[position]);
}

// Function with default behavior
function createTextFrame(x, y, width, height) {
    if (width == undefined) width = 100;
    if (height == undefined) height = 50;
    
    var doc = app.activeDocument;
    var page = doc.pages[0];
    var frame = page.textFrames.add();
    frame.geometricBounds = [y, x, y + height, x + width];
    return frame;
}

// Anonymous function
var multiply = function(a, b) {
    return a * b;
};
```

## Arrays

```javascript
// Creating arrays
var colors = ["Red", "Green", "Blue"];
var numbers = [1, 2, 3, 4, 5];
var mixed = [1, "text", true, null];

// Accessing elements (zero-indexed)
var first = colors[0];   // "Red"
var last = colors[2];    // "Blue"

// Array properties
var length = colors.length;  // 3

// Adding elements
colors.push("Yellow");        // Add to end
colors.unshift("Orange");     // Add to start

// Removing elements
var last = colors.pop();      // Remove from end
var first = colors.shift();   // Remove from start

// Finding elements
var index = colors.indexOf("Green");  // Returns index or -1

// Joining arrays
var allColors = colors.join(", ");  // "Red, Green, Blue"

// Slicing arrays
var subset = colors.slice(1, 3);  // ["Green", "Blue"]

// Looping through arrays
for (var i = 0; i < colors.length; i++) {
    alert(colors[i]);
}
```

## Objects

```javascript
// Creating objects
var book = {
    title: "InDesign Guide",
    pages: 200,
    author: "John Doe",
    published: true
};

// Accessing properties
var title = book.title;           // Dot notation
var pages = book["pages"];        // Bracket notation

// Adding properties
book.isbn = "123-456-789";
book["publisher"] = "Adobe Press";

// Deleting properties
delete book.published;

// Checking if property exists
if ("author" in book) {
    alert("Has author");
}

// Looping through properties
for (var key in book) {
    alert(key + ": " + book[key]);
}
```

## Strings

```javascript
// String methods
var text = "InDesign Scripting";

text.length;                    // 18
text.toUpperCase();            // "INDESIGN SCRIPTING"
text.toLowerCase();            // "indesign scripting"
text.indexOf("Script");        // 9
text.substring(0, 8);          // "InDesign"
text.replace("Script", "Code"); // "InDesign Coding"
text.split(" ");               // ["InDesign", "Scripting"]

// String concatenation
var fullName = "John" + " " + "Doe";

// Checking strings
if (text.indexOf("Design") != -1) {
    alert("Contains 'Design'");
}
```

## Type Conversion

```javascript
// String to Number
var numStr = "42";
var num = parseInt(numStr);      // 42
var float = parseFloat("3.14");  // 3.14

// Number to String
var num = 42;
var str = num.toString();        // "42"
var str2 = String(num);          // "42"
var str3 = "" + num;             // "42"

// Boolean conversion
Boolean(1);        // true
Boolean(0);        // false
Boolean("");       // false
Boolean("text");   // true
```

## Common ExtendScript Limitations

❌ **Not Available:**
```javascript
// No arrow functions
var square = (x) => x * x;  // ✗ Error

// No let/const
let variable = 10;          // ✗ Error
const CONSTANT = 10;        // ✗ Error

// No template literals
var message = `Hello ${name}`;  // ✗ Error

// No spread operator
var arr = [...other];       // ✗ Error

// No modern array methods
colors.map(x => x);         // ✗ Error
colors.filter(x => x);      // ✗ Error
```

✅ **Use Instead:**
```javascript
// Traditional functions
var square = function(x) { return x * x; };

// Use var
var variable = 10;

// String concatenation
var message = "Hello " + name;

// Manual array operations
var newArr = [];
for (var i = 0; i < colors.length; i++) {
    newArr.push(colors[i]);
}
```

## InDesign-Specific Considerations

```javascript
// Collections behave like arrays but aren't true arrays
var pages = doc.pages;
var pageCount = pages.length;

// Access items by index
var firstPage = pages[0];
var lastPage = pages[pages.length - 1];

// Or by name/ID
var page = pages.item(0);
var namedPage = pages.item("A-Master");

// Loop through collections
for (var i = 0; i < doc.pages.length; i++) {
    var page = doc.pages[i];
    // Do something with page
}
```

## Practice Exercises

### Exercise 1: Create a function that counts words in text
```javascript
function countWords(text) {
    var words = text.split(" ");
    return words.length;
}

var sample = "InDesign is a powerful tool";
alert(countWords(sample));  // 5
```

### Exercise 2: Create a color array and format it
```javascript
var colors = ["Red", "Green", "Blue", "Yellow"];
var formatted = "";

for (var i = 0; i < colors.length; i++) {
    formatted += (i + 1) + ". " + colors[i] + "\n";
}

alert(formatted);
/*
1. Red
2. Green
3. Blue
4. Yellow
*/
```

### Exercise 3: Create a document info object
```javascript
function getDocInfo(doc) {
    return {
        name: doc.name,
        pages: doc.pages.length,
        layers: doc.layers.length,
        modified: doc.modified
    };
}

if (app.documents.length > 0) {
    var info = getDocInfo(app.activeDocument);
    alert("Document: " + info.name + "\nPages: " + info.pages);
}
```

## Next Steps

Now that you understand JavaScript basics, proceed to [InDesign Object Model](03-object-model.md) to learn how InDesign's objects work.

## Key Takeaways

✓ Use `var` for all variables (no let/const)  
✓ ExtendScript is ES3 (old JavaScript)  
✓ No arrow functions or modern features  
✓ Use traditional for loops, not forEach/map  
✓ String concatenation with `+` only  
✓ Collections are array-like but not true arrays  
