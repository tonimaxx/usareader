# Lesson 15: Resources and Further Learning

Continue your InDesign scripting journey with these resources.

## Official Documentation

### Adobe InDesign Scripting Guide
**Download:** [Adobe Developer Resources](https://www.adobe.com/devnet/indesign/sdk.html)

The official reference for InDesign scripting. Covers:
- Complete object model
- All properties and methods
- Code examples
- Best practices

Available for download as PDF.

### JavaScript Tools Guide
**Link:** [ExtendScript Documentation](https://extendscript.docsforadobe.dev/)

Essential for understanding ExtendScript (Adobe's JavaScript):
- File system operations
- XML processing
- User interface elements
- Debugging techniques

### InDesign Object Model Viewer
**Where:** Help > Scripting > Object Model Viewer (in InDesign)

Interactive browser for:
- All InDesign objects
- Properties and methods
- Object relationships
- Code examples

**Tip:** Keep this open while scripting for quick reference!

## Online Communities

### Adobe InDesign Forum
**Link:** https://community.adobe.com/t5/indesign/ct-p/ct-indesign

Official Adobe community with:
- Scripting section
- Expert contributors
- Adobe staff responses
- Archives of solved problems

### InDesign Secrets
**Link:** https://indesignsecrets.com/topic/scripting

Popular InDesign resource site:
- Scripting tutorials
- Active forum
- Weekly tips
- Industry experts

### Stack Overflow
**Link:** https://stackoverflow.com/questions/tagged/extendscript

Programming Q&A site:
- Tag: `extendscript`
- Tag: `adobe-indesign`
- Fast responses
- Code examples

### Reddit
**Link:** https://reddit.com/r/indesign

Community discussions:
- r/indesign
- Script sharing
- Problem solving
- Tips and tricks

## Learning Platforms

### LinkedIn Learning
**Courses:**
- "InDesign: Scripting with JavaScript"
- "ExtendScript for Adobe Applications"
- "Programming Foundations: JavaScript"

### Udemy
**Search for:**
- InDesign scripting
- ExtendScript tutorials
- Adobe automation

### YouTube Channels
- **InDesign Secrets** - Regular scripting content
- **Terry White** - Adobe evangelist with scripting tips
- **Nigel French** - InDesign expert with automation focus

## Books

### "Adobe InDesign Automation"
**By:** Dave Saunders

Comprehensive guide to:
- InDesign scripting fundamentals
- Real-world examples
- Advanced techniques
- Production workflows

### "JavaScript: The Good Parts"
**By:** Douglas Crockford

Essential JavaScript knowledge:
- Core concepts
- Best practices
- Common pitfalls
- (Note: Remember ExtendScript is ES3)

### "Scripting InDesign with JavaScript"
**By:** Peter Kahrel

Practical guide covering:
- Beginner to advanced
- Numerous examples
- Production scenarios
- Tips and tricks

**Free download:** Available as PDF from author's website

## Sample Scripts

### Adobe Sample Scripts
**Location:** InDesign installation folder
```
/Applications/Adobe InDesign/Scripts/Scripts Panel/Samples/
```

Includes examples for:
- Text manipulation
- Object creation
- Style management
- Export operations

### GitHub Repositories

**Search for:**
- `indesign scripts`
- `extendscript`
- `jsx scripts`

Popular repositories:
- **indiscripts** - Extensive script collection
- **InDesign-Scripts** - Community contributions
- **Adobe-Scripts** - Various Adobe scripts

## Tools and Utilities

### VS Code Extensions

**ExtendScript Debugger**
- Modern debugging environment
- Breakpoints
- Variable inspection
- Better than ExtendScript Toolkit

**Installation:**
1. Install VS Code
2. Search extensions: "ExtendScript Debugger"
3. Install by Adobe

### Script Launchers

**ScriptUI Panel Builder**
- Create custom panels
- Drag-and-drop interface
- Generate code

**Keyboard Maestro (macOS)**
- Assign keyboard shortcuts to scripts
- Automate script execution
- Workflow automation

**AutoHotkey (Windows)**
- Similar to Keyboard Maestro
- Script shortcuts
- Workflow automation

## Code Snippets Libraries

### Save Commonly Used Functions

Create a personal library file:

```javascript
// my_indesign_library.jsx

// Reusable functions
var MyLib = {
    // Check document
    checkDoc: function() {
        if (app.documents.length == 0) {
            alert("Please open a document!");
            return false;
        }
        return true;
    },
    
    // Get or create style
    getOrCreateStyle: function(doc, name, type) {
        var collection = doc[type + "Styles"];
        try {
            var style = collection.item(name);
            style.name;
            return style;
        } catch (e) {
            var newStyle = collection.add();
            newStyle.name = name;
            return newStyle;
        }
    },
    
    // Safe find/replace
    findReplace: function(doc, findWhat, replaceWith) {
        app.findTextPreferences = NothingEnum.NOTHING;
        app.changeTextPreferences = NothingEnum.NOTHING;
        
        app.findTextPreferences.findWhat = findWhat;
        app.changeTextPreferences.changeTo = replaceWith;
        
        var count = doc.changeText().length;
        
        app.findTextPreferences = NothingEnum.NOTHING;
        app.changeTextPreferences = NothingEnum.NOTHING;
        
        return count;
    }
};

// Include in other scripts:
// #include "my_indesign_library.jsx"
// if (!MyLib.checkDoc()) exit();
```

## Websites and Blogs

### InDesignSecrets.com
- Weekly scripting tips
- Script downloads
- Expert interviews
- Industry news

### CreativePro.com
- InDesign tutorials
- Scripting articles
- Production tips
- Expert columnists

### InDesign User Group (IDUG)
- Local chapters worldwide
- Online meetings
- Script sharing
- Networking

### Kahrel.plus.com
**Peter Kahrel's site**
- Free scripts
- Scripting guide (PDF)
- Regular updates
- Excellent resource

## Stay Updated

### Adobe Blog
**Link:** https://blog.adobe.com

Updates on:
- New features
- API changes
- Best practices
- Case studies

### InDesign Release Notes
Check with each version for:
- New scripting features
- API changes
- Bug fixes
- Deprecated features

### Twitter/X
Follow:
- @AdobeInDesign
- @InDesignSecrets
- @CreativePro
- #InDesignScripting

## Practice Projects

### Beginner Projects
1. **Page Numbering Tool** - Add page numbers to all pages
2. **Text Finder** - Search and highlight specific text
3. **Color Swatches Creator** - Import colors from list
4. **Document Reporter** - Generate document statistics

### Intermediate Projects
1. **Style Organizer** - Sort and clean up styles
2. **Image Processor** - Batch resize and fit images
3. **Table Generator** - Create tables from CSV data
4. **Template Applier** - Apply master pages systematically

### Advanced Projects
1. **Data Merge System** - Complete mail merge solution
2. **Export Manager** - Multi-format export tool
3. **Style Mapper** - Convert between style systems
4. **Production Dashboard** - Real-time document analysis

## Contribute to Community

### Share Your Scripts
- GitHub repositories
- Adobe forums
- InDesign Secrets
- Personal blog

### Write Tutorials
Document your learning:
- Blog posts
- YouTube videos
- Forum answers
- GitHub wikis

### Answer Questions
Help others learning:
- Adobe forums
- Stack Overflow
- Reddit
- Twitter

## Certification and Training

### Adobe Certified Professional
**Program:** Adobe Certified Professional in InDesign

While not specifically for scripting, demonstrates expertise:
- InDesign mastery
- Professional workflows
- Industry recognition

### Custom Training
Many organizations offer:
- InDesign scripting workshops
- Corporate training
- One-on-one coaching
- Online courses

## Keep Learning

### Regular Practice
- Write scripts weekly
- Automate your own tasks
- Experiment with new features
- Refactor old scripts

### Study Others' Code
- Read sample scripts
- Analyze GitHub repos
- Understand different approaches
- Learn best practices

### Challenge Yourself
- Solve real problems
- Optimize for performance
- Add error handling
- Create user interfaces

### Stay Curious
- Explore new InDesign features
- Learn related technologies (XML, GREP)
- Understand design workflows
- Ask "can this be scripted?"

## Quick Reference Checklist

When starting a new script project:

- [ ] Check InDesign version compatibility
- [ ] Review similar scripts for inspiration
- [ ] Plan script logic on paper first
- [ ] Test on copy documents
- [ ] Add error handling
- [ ] Include user feedback
- [ ] Document your code
- [ ] Test edge cases
- [ ] Share with community
- [ ] Update based on feedback

## Final Tips

### Be Patient
- Scripting takes practice
- Start simple, build complexity
- Learn from mistakes
- Don't give up!

### Stay Organized
- Keep scripts well-commented
- Use descriptive names
- Maintain a personal library
- Version control (Git)

### Think Like a Developer
- Break problems into steps
- Test incrementally
- Handle errors gracefully
- Optimize for performance

### Think Like a Designer
- Create intuitive interfaces
- Provide clear feedback
- Confirm destructive actions
- Make scripts user-friendly

## Recommended Learning Path

**Week 1-2: Foundations**
- Review JavaScript basics
- Study InDesign object model
- Run sample scripts
- Modify existing scripts

**Week 3-4: Basic Scripts**
- Create simple automation
- Work with text and frames
- Implement error handling
- Add user input

**Week 5-6: Intermediate**
- Work with styles
- Manipulate pages
- Process multiple documents
- Create reusable functions

**Week 7-8: Advanced**
- Build complex workflows
- Optimize performance
- Create UI panels
- Handle edge cases

**Ongoing:**
- Automate your real work
- Share with community
- Learn from others
- Keep improving

## Contact and Support

### Need Help?
1. Search Adobe forums
2. Check Stack Overflow
3. Review documentation
4. Post detailed questions
5. Join InDesign communities

### Report Bugs
**Adobe Bug Reporter:** 
https://www.adobe.com/products/wishform.html

### Feature Requests
**Adobe UserVoice:**
Vote for scripting improvements

## Course Complete!

Congratulations on completing the InDesign Scripting Quick Guide!

You now have:
✓ JavaScript fundamentals
✓ InDesign object model knowledge
✓ Practical examples and code
✓ Debugging skills
✓ Best practices understanding
✓ Resources for continued learning

**Keep scripting and automating!**

---

## Additional Resources Links

- [Adobe InDesign SDK](https://www.adobe.com/devnet/indesign/sdk.html)
- [ExtendScript Documentation](https://extendscript.docsforadobe.dev/)
- [InDesign Secrets](https://indesignsecrets.com)
- [Adobe Forums](https://community.adobe.com/t5/indesign/ct-p/ct-indesign)
- [Peter Kahrel Scripts](https://www.kahrel.plus.com)
- [GitHub InDesign Scripts](https://github.com/topics/indesign-scripts)

---

*Last Updated: February 2026*
