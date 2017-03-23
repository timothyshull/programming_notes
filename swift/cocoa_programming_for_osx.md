1. Let’s Get Started
Creating an Xcode Project
Getting around in Xcode
Application Design
Model-View-Controller
Creating the MainWindowController class
Creating the User Interface in Interface Builder
Adding view objects
Configuring view objects
XIB files and NIB files
Showing the Window
Making Connections
Creating an outlet
Connecting an outlet
Defining an action method
Connecting actions
Creating the Model Layer
Connecting the Model Layer to the Controller
Improving Controller Design

2. Swift Types
Introducing Swift
Types in Swift
Using Standard Types
Inferring types
Specifying types
Literals and subscripting
Initializers
Properties
Instance methods
Optionals
Subscripting dictionaries
Loops and String Interpolation
Enumerations and the Switch Statement
Enumerations and raw values
Exploring Apple’s Swift Documentation

3. Structures and Classes
Structures
Instance methods
Operator Overloading
Classes
Designated and convenience initializers
Add an instance method
Inheritance
Computed Properties
Reference and Value Types
Implications of reference and value types
Choosing between reference and value types
Making Types Printable
Swift and Objective-C
Working with Foundation Types
Basic bridging
Bridging with collections
Runtime Errors
More Exploring of Apple’s Swift
Documentation
Challenge: Safe Landing
Challenge: Vector Angle



# 4. Memory Management
- value type memory is reclaimed when the object goes out of scope
- value types that are members of reference types are reclaimed when the
  containing reference type is reclaimed
- reference counting is used for reference types to manage object
  lifetime
## Automatic Reference Counting
- addresses these needs:
    - objects need to stay in memory as long as needed
    - objects should be deallocated as soon as they are no longer needed
- mac OS uses ARC (automatic reference counting) to do it
## Objects have reference counts
- creation increments reference count to 1
- when assigned or passed elsewhere, reference count is incremented
- when property, outlet, or variable changes or becomes nil, reference
  count is decremented
## Deallocating objects in a hierarchy
- each container that contains a reference to another container
  maintains the contained references hierarchically
- when the reference type at the top of the hierarchy is deinitialized,
  all reference types down the hierarchy chain are deinitialized
- actual container types, such as Array, are not reference types but
  do maintain the lifetime of contained reference types if the types
  they contain are reference types
## Strong and Weak References
- by default, a reference is a strong reference
- an object's reference count only refers to strong references
- a weak reference permits access to a reference type but does not
  increment the object's reference count
- weak references are always optional types
## Strong reference cycles
- strong reference cycle - two objects have references to each other
  directly or indirectly
- a strong reference cycle causes a portion of the object graph to be
  leaked because none of the objects in the cycle can be deallocated
- ARC is part of the compiler and is incapable of analyzing the runtime
  behavior and thus incapable of detecting a strong reference cycle
- commonly use weak references in:
    - a reference to a parent in a hierarchical type relationship
    - outlets to subviews within a window controller -> the window
      indirectly has strong references to the view hierarchy and the
      window controller has a strong reference to the window
    - targets from controls -> controller has indirect strong
      references to its controls, control should have weak references
      back to the controller where action method is implemented
    - delegates ->
## Unowned references
- non-strong, non-optional reference
- does not increment reference count
- not set to nil when deallocated
- used to make Cocoa compatible with ARC
- also sometimes used in closures
- only use when you know the referenced object will not be accessed
  after the object has been deallocated
## What is ARC?
- previous approach was manual retain and release
- Cocoa added garbage collection in 2007 which had drawbacks
- Clang static analyzer facilitated ARC
- promotes optimized, automatic retain and release calls generated
  within compiler that is less error prone

### Advanced Memory Management (Apple Docs)
- process of allocating memory during runtime, using it, and freeing it
- in Objective-C, can be used to distribute limited resources manually
- goal is manage object graphs (i.e. complex interconnections between
  objects at runtime
- Objective-C provides two methods of application memory management:
    - manual retain-release -> explicitly manage memory using reference
      counts (managed by NSObject and runtime environment)
    - ARC -> inserts memory management method calls at compile-time
### Good Practices Prevent Memory-Related Problems
- problems:
    - premature free -> causes memory corruption, program crash,
      corrupted user data
    - not freeing data -> causes memory leaks which reduce performance
      and eventually can cause program termination
- better to think of memory management from the perspective of object
  ownership and object graphs instead of reference counts

### Use Analysis Tools to Debug Memory Problems
- use Clang static analyzer in Xcode
- additionally use NSZombie and other tools (see additional guides)

### Memory Management Policy
- uses methods in NSObject protocol
#### Basic Memory Management Rules
- based on object ownership
- Cocoa's policy:
    - you own any object you create (alloc, new, copy, mutableCopy,
      newObject)
    - take ownership using retain
        1. in init -> to store an object as a property value
        2. avoid deallocation as a side-effect of another operation
    - release when ownership is no longer needed (use release or
      autorelease)
    - do not release an unowned object
- retain and release are just Objective-C method calls (messages)
- objects returned by reference are not owned
    - in Objective-C inout parameters with ClassName** or id*
- implement dealloc to release ownership
    - free object's own memory
    - dispose held resources, including object instance variables
- never manually invoke another object's dealloc method
- Core Foundation uses similar but different rules
    - see the related guide
### TODO: more here

# 5. Controls
- most other GUI frameworks work by long chains of subclassing
  view types
- Cocoa applications prefer creating objects that work with existing
  classes and connecting them together
## Setting up RGBWell
- create Xcode project without Core Data
- select Swift or Objective-C
## Creating the MainWindowController class
- cmd-N, select Swift file
```
import Cocoa

class MainWindowController: NSWindowController {}
```
- Cocoa -> Foundation, AppKit, CoreData
## Creating an empty XIB file
- cmd-N and select Empty UI file
- use object library to add a window
- refer to document outline for window contents
- use attribute inspector to uncheck "Visible at Launch"
- "Visible at Launch" duplicates some code and masks the relationship
  between WindowController and Window
- suggested to uncheck as a matter of practice
- generally does not cause problems except when working with Sheets
- remove window from MainMenu.xib
## Creating an instance of MainWindowController
- maintains a reference to the window controller to keep the window
  controller from being deinitialized
```
class AppDelegate: NSObject, NSApplicationDelegate {
    var mainWindowController: MainWindowController?
    func applicationDidFinishLaunching(aNotification: NSNotification) {
        // Create a window controller
        let mainWindowController = MainWindowController()
        // Put the window of the window controller on screen
        mainWindowController.showWindow(self)
        // Set the property to point to the window controller
        self.mainWindowController = mainWindowController
    }
}
```
## Connecting a window controller and its window
- MainWindowController inherits a window outlet from NSWindowController
- this outlet must be connected to a window object in a XIB
- NSWindowController's window can be loaded from a NIB or created
  programmatically
- need to set name of window controller's NIB file in the class
- when the NIB file is loaded, the window object is unarchived
- the NIB is loaded as a side effect of accessing the window property
  for the first time (applicationDidFinishLaunching -> showWindow ->
  window is nil so NIB is loaded)
- object exists after window is loaded, showWindow can only complete
  if the window outlet is connected to the window object
- this process requires the File Owner setting in the XIB file
- XIB is XML repr of objects and connections, both of which must be
  represented at either end
- can sometimes make connections on each end directly within XIB
- often need to connect to to an object outside of the XIB which
  requires placeholders and File's Owner
- File's Owner is a placeholder that stands in for the object that will
  load the NIB file at runtime, generally the controller
- to set, select File's Owner in the document outline, select the icon
  inspector, find the class attribute and enter the class name of the
  controller class
- this permits outlet and action connections from the XIB file the
  file's owner class
- after setting the File's Owner, the window outlet in the file's owner
  must be connected to the window within the XIB file
```
MainWindowController: NSWindowController
    - windowNibName = "MainWindowController"
    - window

    |
    v

XIB file
    - File's Owner
        - class attribute = MainWindowController
        - Outlet
            |
            v
    - window in XIB file
```
## About Controls
- control -> view designed for user interaction that is a subclass of
  NSControl
```
NSObject
^
|
NSResponder (event handling)
^
|
NSView (window display -> drawRect, etc)
^
|
NSControl (ability to send action messages)
    - target
    - action
    - enabled
    - continuous
    - objectValue
    - stringValue
    - floatValue
    - integerValue
^
|
NSButton & NSTextField & NSSlider
```
- adds the ability to send action messages to a target
- action -> a selector sent to the target of the action message
- selector -> essentially the name of a method in a string form
## Working with Controls
- same class may have a number of different visual styles
## A word about NSCell
- each control comes with a subclass of NSCell
- NSCell handles the implementation details of the control
- NSCell has properties that are hooked up to the control's properties
- cell's are being deprecated but still remain
## Connecting the slider’s target and action
- must create an IBAction method within the file's owner class
- each IBAction has a single sender parameter
- can implement that sender as the control's class or AnyObject
- use AnyObject for situations where you have multiple types of controls
  triggering the same action method
- action methods appear in the context menu of the File's Owner within
  the XIB file and the action sent by a control can be connected using
  ctrl+drag to the File's Owner Received Actions menu within its
  context menu
- NSControl defines its target outlet as a weak reference
## A continuous control
- the continuous property on a controller is false by default and an
  action message is only sent at interaction end
- when continuous = true, the action message is sent continuously
- set the continuous attribute in the attributes inspector
## Setting the slider’s range values
- set min and max in the attributes inspector
## Adding two more sliders
## NSColorWell and NSColor
- color well by default opens a color picker on click
## Disabling a control
- each control has an enabled property
- setting enabled = false means the control does not respond to user
  interaction
- can set in the attributes inspector
## Using the Documentation
- Xcode contains API documentation and guides
## Changing the color of the color well
- requires getting the Double value from the slider sender and
  converting to CGFloat to pass to an NSColor init method
## Controls and Outlets
- to access a control outside of an action method, must create an outlet
  and connect the control to the outlet
```
@IBOutlet weak var rSlider: NSSlider!
```
- good habit to call super.windowDidLoad() within overridden
  windowDidLoad (and other overridden methods)
## Implicitly unwrapped optionals
- optional that never needs to be unwrapped (i.e. forcibly unwrapped)
- outlets should always be implicitly unwrapped
- outlets must be optional because they must be loaded from the NIB
- these will never be nil if the NIB is properly loaded and the
  connections are properly made
- a missing connection will cause the app to crash
## For the More Curious: More on NSColor
- has class methods that return common colors
- also has methods for common systems colors
    - controlTextColor
    - selectedTextBackgroundColor
    - windowBackgroundColor
- see guide Color Management Overview
## For the More Curious: Setting the Target Programmatically
- NSControl has an action property which is a Selector
- to set programmatically
```
var playButton: NSButton
func play(sender: NSButton) {}
let playSelector = Selector("play:")
playButton.action = playSelector
```

## Challenge: Busy Board Debugging Hints
- hints:
    - can double-click an object in the object library to see the
      object's class and some other details
    - NSButton has a state property which can be used for checkboxes
    - the Radio Group is based on NSMatrix which is informally
      deprecated
    - radio groups can be implemented programmatically using their
      Style property and NSView's tag property

# Debugging Hints
- always watch the console
- always use the debug build configuration during development
- common problems:
    - crash when programmatically using a view set up using IB
        - missing connection
    - connection was made but app still crashes
        - possible misspelling or other issue invalidating connection
    - app crashes
        - accessing a deallocated object
        - can use NSZombie and zombies to debug
    - cannot make a connection in IB
        - issue in Swift file syntax

# 6. Delegation
## Setting up SpeakLine
- create Xcode Cocoa project
- remove default window in XIB file
- create MainWindowController class and XIB file, unset show at start,
  connect File's Owner
- add default code
```
// MainWindowController.swift
class MainWindowController: NSWindowController {
    override var windowNibName: String {
        return "MainWindowController"
    }
    override func windowDidLoad() { super.windowDidLoad() }
}
// AppDelegate.swift
class AppDelegate: NSObject, NSApplicationDelegate {
    var mainWindowController: MainWindowController?
    func applicationDidFinishLaunching(aNotification: NSNotification) {
        // Create a window controller
        let mainWindowController = MainWindowController()
        // Put the window of the window controller on screen
        mainWindowController.showWindow(self)
        // Set the property to point to the window controller
        self.mainWindowController = mainWindowController
    }
}
```
## Creating and using an Xcode snippet
- can add code snippets within the snippet library window in Xcode
- can click and drag code snippets to use them
## Creating the user interface
- add buttons and text fields and create outlets within controller
## Synthesizing Speech
- NSSpeechSynthesizer
    - startSpeakingString(_ text: String!) -> Bool
    - stopSpeaking()
## Updating Buttons
- programmatically enable and disable buttons using the enabled property
## Delegation
- objects that have specified delegate protocols are defined to have
  the delegate as a helper object
## Being a delegate
- being a delegate means taking on a rol specified by a protocol and
  conforming (inheriting and implementing specified methods) from that
  protocol
- passes on behavior while avoiding need to subclass
- optional keyword within a protocol indicates that a conforming class
  is not required to implement the method
- to conform, inherit from class and implement all required methods
- each class that has a corresponding delegate class also has a
  delegate property
- to assign
```
override func windowDidLoad() {
    super.windowDidLoad()
    delegatee.delegate = self
}
```
- no delegate properties are strong references
## Implementing another delegate
- classes can function as delegates for many properties
## Common errors in implementing a delegate
- forgetting to set the delegate property
    - since it is optional, the compiler will not provide a warning if
      it is not set
- misspelling the name of an optional delegate method
## Cocoa classes that have delegates
- AppKit
    - NSAlert 
    - NSMatrix 
    - NSTabView
    - NSAnimation
    - NSMenu
    - NSTableView
    - NSApplication
    - NSPathControl
    - NSText
    - NSBrowser
    - NSRuleEditor
    - NSTextField
    - NSDatePicker
    - NSSavePanel
    - NSTextStorage
    - NSDrawer
    - NSSound
    - NSTextView
    - NSFontManager
    - NSSpeechRecognizer
    - NSTokenField
    - NSImage
    - NSSpeechSynthesizer
    - NSToolbar
    - NSLayoutManager
    - NSSplitView
    - NSWindow

## Delegate protocols and notifications
- many delegate methods take NSNotification as a parameter
- sent via notification center rather than being called by the object
  on delegate object
## NSApplication and NSApplicationDelegate
- each Cocoa app has a single instance of NSApplication and asingle
  instance of a custom class named AppDelegate
- AppDelegate methods are triggered by application lifecycle events
- @NSApplicationMain -> instantiates NSApplication and AppDelegate and
  sets NSApplication's delegate property to AppDelegate
## The main event loop
- NSApplication starts and maintains main event loop
- when a UI event occurs, the event is forwarded from the window
  server to the corresponding NSApplication instance and is added to
  the app's event queue which is then forwarded to the proper event
  handler when the event is processed

# NOTE: a large part of learning Cocoa is learning the proper methods to
  implement -> action methods, delegate methods, and notification
  observers

## For the More Curious: How Optional Delegate Methods Work
- only calls implemented optional delegate methods which are note called
  otherwise
- this is facilitated in Swift using optionals and the short circuits
  that happen is optionals are nil as well as optional method calls
  (.didFinishSpeaking?)
## Challenge: Enforcing a Window’s Aspect Ratio
- use windowWillResize, CGSize typealiased to NSSize, and the initial
  size in the size inspector of Interface Builder

# 7. Working with Table Views
## About Table Views
- a table view is used to display data arranged in rows and
  columns
## Delegates and data sources
- a table view can have both a delegate and a data source
- typically one object plays both roles for a table view
- for a table view, the delegate must conform to the NSTableViewDelegate
  protocol
- a data source is like a delegate except it interacts with a model (or other
  data repository manager)
- for table view, the data source must conform to NSTableViewDataSource
- like for the delegate, the table view has a dataSource property which must
  be set to the data source instance
## The table view-data source conversation
- the table view controls requests for data from the data source
- the table view asks for data like an iterator, one element at a time, e.g. one
  row x column location at a time
- methods:
    - numberOfRowsInTableView
    - tableView
## SpeakLine’s table view and helper objects
- uses MainWindowController as both the data source and the delegate
## Getting Voice Data
- NSSpeechSynthesizer
    - class func availableVoices which returns `[NSString]` but should cast
      to Swift String
## Retrieving friendly names
- NSSpeechSynthesizer
    - class func attributesForVoice -> optional dictionary
## Adding a Table View
- table view is available in the object library
## Table view and related objects
- table view is a collection of many objects
    - clip view
        - table view
            - table column
            - table column
            - etc.
        - scroller
        - scroller
        - table header view
- the scroll view is there because a table view can have more data than
  it can display
- clip view is an instance of NSClipView and manages the currently visible portion
  of the table and contains the table view
- scrollers control table scroll
- table header is NSTableHeaderView and responds to mouse clicks on the titles
- NSTableColumn
    - change title in attributes inspector
- to disable resize, uncheck resize in attributes inspector
## Tables, Cells, and Views
- important to know that cell-based tables, those based on NSCell, have been deprecated
- newer tables are view-based, i.e. work by using NSView
## Table cell views
- text table cell view - contains a single text field
    - Table Cell View - NSTableCellView
        - Table View Cell - NSTextField
            - Table View Cell - NSTextFieldCell
- image & text table cell view - text field and image view
- can create custom table cell views
- setting the objectValue property of the table cell view does not affect
  what the table displays, so must change the text field when objectValue is
  updated using bindings
## The NSTableViewDataSource Protocol
## Conforming to the protocol
- inherit from the class
## Connecting the dataSource outlet
- click the table view in the XIB file and ctrl-drag to the File's Owner
## Implementing data source methods
```
func numberOfRowsInTableView(tableView: NSTableView) -> Int { return voices.count }
func tableView(
    tableView: NSTableView,
    objectValueForTableColumn tableColumn: NSTableColumn?,
    row: Int
) -> AnyObject? {
    let voice = voices[row]
    let voiceName = voiceNameForIdentifier(voice)
    return voiceName
}
```
## Binding the text field to the table cell view
- use the bindings inspector and select text field in XIB
- under bind to select Table Cell View
- Model Key Path should be objectValue -> property of table cell view
## The NSTableViewDelegate Protocol
- drag from table view connections pop-up menu to File's Owner
## Making a connection with the assistant editor
- can also use the assistant editor to make connections
- can drag to the related file from a view and create an outlet automatically
## Implementing a delegate method
- the table view's selectedRow property contains the necessary information
- the delegate method required is tableViewSelectionDidChange
```
// MARK: - NSTableViewDelegate
func tableViewSelectionDidChange(notification: NSNotification) {
    let row = tableView.selectedRow
    // Set the voice back to the default if the user has deselected all rows
    if row == -1 {
        speechSynth.setVoice(nil)
        return
    }
    let voice = voices[row] speechSynth.setVoice(voice) }
}
```
## Pre-selecting the default voice
- within windowDidLoad, get NSSpeechSynthesizer's defaultVoice
```
let defaultVoice = NSSpeechSynthesizer.defaultVoice()
if let defaultRow = find(voices, defaultVoice) {
    let indices = NSIndexSet(index: defaultRow)
    tableView.selectRowIndexes(indices, byExtendingSelection: false)
    tableView.scrollRowToVisible(defaultRow)
}
```
- see the Table View Programming Guide for more info
## Challenge: Make a Data Source
- use an add button, an array property in the controller, and the table view's
  reloadData method

# 8. KVC, KVO, and Bindings
- Key-value coding is a mechanism that allows the programmer to get and
  set the value of a property indirectly using a key, i.e. the name of the
  property as a string
- core data and bindings rely on KVC
- setting:
    - object.setValue("Value", forKey: "keyName")
- getting:
    - object.valueForKey("keyName")
## Bindings
- bindings can be used to reduce/eliminate the code to link the view to the model
## Setting up Thermostat
- simple setup
- add vertical slider, label, and warmer and cooler buttons
## Using bindings
- add a temperature property to MainWindowController
- in bindings inspector for the slider
    - Bind to -> File's Owner
    - Model Key Path -> temperature
- do the same for the label
- this uses KVC and KVO behind the scenes
## Key-value observing
- object registers to observe changes to the value of a key
- can add action methods that manually make changes and KVO still happens
  as long as the changes are made in a KVO compliant manner
## Making keys observable
- by default Swift properties are not KVO-compliant
- two options:
    - add the keyword dynamic
```
dynamic var keyName
```
    - explicitly trigger observers on change
```
willChangeValueForKey("keyName")
// do something
didChangeValueForKey("keyName")
```
- more common to see dynamic keyword
- dynamic keyword makes a property behave like an Objective-C property, thus
  employing the Objective-C runtime
## Binding other attributes
- add a switch isOn and bind to power button
## KVC and Property Accessors
- KVC will call property accessors (custom getters and setters) if present
```
dynamic var varName: Int {
    set { print() varName = newValue }
    get { print() return otherValue }
}
```
- common to use a computed value (as above) with a private property to control access
## KVC and nil
- when a value is set to nil, no assumption is made about how to assign that value
- KVC calls the method setNilValueForKey(_:) (which throws for NSObject)
- nil is sent for an empty text field
- many optional types will work
- optional numeric types can cause a crash, which can be worked around by using
  NSNumber?
## Debugging Bindings
- notoriously difficult to debug
- common problem
    - key name typo
- to reveal all bindings for an object, show the the connections pop-up and look
  at the bindings section at the bottom
## Using the Debugger
## Using breakpoints
- can refer to the debug navigator in the navigator area on the left
## Stepping through code
- step buttons left to right
    - show/hide debug area
    - toggle all breakpoints
    - continue/pause
    - step over line
    - step into method
    - step out of method
- to set an exception breakpoint, add breakpoint in breakpoint navigator and
  select exception breakpoint
## The LLDB console
## Using the debugger to see bindings in action
## For the More Curious: Key Paths
- objects are arranged in a network within a program
- key paths extend beyond a single object and can specify a path through a network
  of objects
- key paths can include operators
    - @avg
    - @count
    - @max
    - @min
    - @sum
- to bind programmatically:
```
textField.bind(
    NSValueBinding, // "value"
    toObject: employeeController,
    withKeyPath: "arrangedObjects.@ avg.expectedRaise",
    options: nil
)
```
- use unbind(_:) to remove the binding
```
textField.unbind(NSValueBinding)
```
## For the More Curious: More on Key-Value Observing
- programmatic observer (from NSObject):
```
let mainWindowController = ...
mainWindowController.addObserver(
    self,
    forKeyPath: "temperature",
    options: NSKeyValueObservingOptions.Old,
    context: &MyKVOContext
)
```
- triggered method
```
override func observeValueForKeyPath(
    keyPath: String!,
    ofObject object: AnyObject!,
    change: [NSObject : AnyObject]!,
    context: UnsafeMutablePointer<Void>
) { ... }
```
## For the More Curious: Dependent Keys
- for dependent keys, define a class method named keyPathsForValuesAffecting?KeyName?()
  that returns a set of key paths
```
class func keyPathsForValuesAffectingFullName() -> NSSet { return Set([" firstName", "lastName"]) }
```
## Challenge: Convert RGBWell to Use Bindings
- often useful to state what you are binding to determine how to do it
- use default values for properties to avoid defining overriding init
- for NSColor, use the class methods to supply the default property values
- does not require outlets

# 9. NSArrayController
- create a document based app -> subclass of NSDocument which has a reference
  to the data which makes up the document
- setup as usual but add document extension in initial options of rsmn
## RaiseMan’s Model Layer
- uses an Employee class -> is a model so only needs Foundation
```
class Employee: NSObject {
    var name: String? = "New Employee"
    var raise: Float = 0.05
}
```
- Document (view controller in document-based app) contains a collection of employees
- temporarily comment out error lines in dataOfType and readFromData
## RaiseMan’s View Layer
- table view, two buttons
## Introducing NSArrayController
- NSArrayController is another type of view controller that sit between the Document
  class view controller and its table view
- works by using bindings
- properties:
    - content -> the input to the array controller (rarely used directly, most
      often used by binding name "Content Array", which gets bound to a model
      array)
    - arrangedObjects -> output, typically on receiving end of a binding
    - selectionIndexes
- think of array controller as a series of directed components combined
- direct towards an array by binding, array control filters, then runs result
  of filtering through the sorting (arrangedObjects)
- array controllers also handle selection -> arrangedObjects may not map content
  so selectionIndexes can map between the two
- NSPopUpButton and NSComboBox can also be bound to an array controller
## Adding an Array Controller to the XIB
- can just drag-and-drop to canvas
- under attributes inspector, select RaiseMan.Employee as the Class Name under
  Object Controller
## Binding the Array Controller to the Model
- Bind to -> File's Owner + X
- Controller Key -> ""
- Model Key Path -> employees
## Binding the Table View’s Content to the Array Controller
- goto Table Content section for the Table View
- Content -> Array Controller's arrangeObjects
- Model Key Path -> ""
## Connecting the Add Employee Button
- ctrl-drag from Add Employee button and set section to add:
## Binding the Text Fields to the Table Cell Views
- as before, each cell in a row of a table view is an NSTableCellView with
  an objectValue
- any control within that must be bound to the value of a property on that
  object
- select text field in first column's table cell view (NSTextField and not
  NSTextFieldCell)
- first column
    - Value -> Bind to -> Table Cell View
    - Controller Key -> ""
    - Model Key Path -> objectValue.name
- second column
    - Value -> Bind to -> Table Cell View
    - Controller Key -> ""
    - Model Key Path -> objectValue.raise
## Formatting the Raise Text Field
- drag a number formatter onto text field cell of second column
- attributes inspector -> Style -> Percentage & X Lenient
## Connecting the Remove Button
- ctrl-drag from the remove button to Array Controller and set the action
  to remove:
- the remove action does not know how to map which row is selected at this point
## Binding the Table View’s Selection to the Array Controller
- the selection indexes are the indexes of the arrangedObjects at this point
- selection indexes are not indexes in the array controller's content array
- Bindings ->
    - Selection Indexes ->
        - Bind to -> Array Controller's selectionIndexes
    - Model Key Path -> ""
## Configuring RaiseMan’s Remove Button
- want to disable remove when no employee is selected
- Bindings ->
    - Availability ->
        - Enabled ->
            - Bind to -> Array Controller
        - Controller Key -> canRemove
- Attributes Inspector -> Key Equivalent -> Delete Key
## Sorting in RaiseMan
- clicking on Column Headers changes the sort descriptors (NSSortDescriptor)
- each sort descriptor contains a key (KVC path), ascending vs. descending,
  comparison selector (comparator)
- in a table view, each column corresponds to a sort descriptor
- select Raise column -> Attributes Inspector -> Sort Key -> raise
- automatically sets Selector to compare: (method selector that is comparator,
  NSNumber's compare method)
- select Name column -> Attributes Inspector -> Sort Key -> name
    - Selector -> caseInsensitiveCompare: (method on String)
- select Table View -> Bindings Inspector
    - Table Content
        - Sort Descriptors -> Array Controller
        - Controller Key -> sortDescriptors
        - Model Key Path -> ""
## How Sorting Works in RaiseMan
- often makes sense to apply multiple sort descriptors at once
## For the More Curious: The caseInsensitiveCompare(_:) Method
- returns an NSComparisonResult
    - OrderedAscending - a b
    - OrderedSame - b == a
    - OrderedDescending - b a
## For the More Curious: Sorting Without NSArrayController
- can use sortedArrayUsingDescriptors
```
func tableView(
    tableView: NSTableView,
    sortDescriptorsDidChange oldDescriptors: [AnyObject]
) {
    let sortDescriptors = tableView.sortDescriptors
    let objectsArray = objects as NSArray
    let sortedObjects = objectsArray.sortedArrayUsingDescriptors(sortDescriptors)
    objects = sortedObjects tableView.reloadData()
}
```
## For the More Curious: Filtering
- NSArrayController does filtering and sorting
    - filterPredicate (NSPredicate) -> see Predicate Programming Guide
    - sortDescriptors (array of NSSortDescriptor objects)
- predicates rely on KVC so for large arrays prefer Array<T>.filter(_:)
```
let matches = employees.filter { employee -> Bool in
    if let name = employee.name {
        let nameMatches = name.caseInsensitiveCompare(someName) == .OrderedSame
        return nameMatches && employee.raise > 0.0
     } else { return false }
}
```

## For the More Curious: Using Interface Builder’s View Hierarchy Popover
- related to jump bar for hierarchical drill down
- for the popover -> ctrl+shift+click
## Challenge: Sorting Names by Length
- can use the .length property on the names

# 10. Formatters and Validation
- provide validation at the controller layer
- Key-Value Validation provides validation at the model layer
## Formatters
- NSNumberFormatter, NSDateFormatter
- for dates, if there is a specific input string format, specify it, otherwise
  use defaults to take advantage of locale settings
## Formatters, programmatically
```
let date = NSDate() // "Feb 28, 2015, 8: 07 PM"
let dateFormatter = NSDateFormatter() //
dateFormatter.dateStyle = .MediumStyle
dateFormatter.timeStyle = .ShortStyle
dateFormatter.stringFromDate(date) // "Feb 28, 2015, 8: 07: 59 PM"

let ransom = 1_000_000 // 1000000
let numberFormatter = NSNumberFormatter()
numberFormatter.numberStyle = .CurrencyStyle
numberFormatter.stringFromNumber(ransom) // "$ 1,000,000.00"
numberFormatter.maximumFractionDigits = 0
numberFormatter.stringFromNumber(ransom) // "$1,000,000"
```
## Formatters and a control’s objectValue
- formatters generally straddle the view and controller layer
- user input goes through the formatter when a formatter is added to a text field
- when the objectValue is set on a text field with a formatter, the formatter
  will convert the object to a string representation, which is available as stringValue
## Formatters and localization
- formatters have access to the locale settings
- there are more formatters in Cocoa than mentioned here
## Validation with Key-Value Coding
- can use Key-Value Coding Validation at the model and/or controller layer
## Adding Key-Value validation to RaiseMan
- as is, the formatter with empty input will throw an unhandled exception
    1. enter empty string
    2. formatter transforms input to optional NSNumber which is nil
    3. no validation is done between the binding and the model
    4. bindings sets Employee.raise to nil
    5. NSNumber does not know how to handle nil and throws an exception
- to enable validation for a binding
    - Table Cell View -> Bindings Inspector
        - Validate Immediately -> X
- the format of a method for validation in this case is validate?KeyName?(_:error:)
```
func validateRaise(
    raiseNumberPointer: AutoreleasingUnsafeMutablePointer<NSNumber?>,
    error outError: NSErrorPointer
) -> Bool
```
- true means that the value is valid
- can change the value and the error within the method since they are pointers
```
func validateRaise(
    raiseNumberPointer: AutoreleasingUnsafeMutablePointer<NSNumber?>,
    error outError: NSErrorPointer
) -> Bool {
    let raiseNumber = raiseNumberPointer.memory
    if raiseNumber == nil {
        let domain = "UserInputValidationErrorDomain"
        let code = 0
        let userInfo = [NSLocalizedDescriptionKey : "An employee's raise must be a number."]
        outError.memory = NSError(domain: domain, code: code, userInfo: userInfo)
        return false
    } else { return true }
}
```
## For the More Curious: NSValueTransformer
- NSValueTransformer can be used with bindings to modify the value read by the binding
  from an object
- Bindings Inspector -> Value Transformer
- one type is NSNegateBoolean
- can subclass NSValueTransformer
- see Value Transformer Programming Guide

# 11. NSUndoManager
- NSUndoManager maintains two stacks, one for undo and one for redo
- objects on stacks are NSInvocation
## Message Passing and NSInvocation
- message passing is a dynamic, runtime polymorphism mechanism that allows
  the method that is called to be determined when the message is passed
- if an object does not understand a received message it:
    - checks for a forwardInvocation(_:) method
    - throws an exception if it does not have it
- Swift adds its own adaptation of the Objective-C runtime to handle this when
  necessary
## How the NSUndoManager Works
- uses undo and redo stack
## Using NSUndoManager
- by default all invocations added to the stack during a single event are grouped
  together
- each NSDocument already has its own undo manager
## Key-Value Coding and To-Many Relationships
- RaiseMan requires redoing and undoing adding employees
- Document is not changing employees, NSArrayController is
- employees property represents and ordered to-many relationship on Document
- KVC recognizes the following relationships:
    - simple attributes -> typically strings, numbers, dates, etc
    - To-one relationships -> reference type, i.e. class/custom class
    - ordered to-many -> collection with ordering (inherent to relationship, not display)
    - unordered to-many -> collection without inherent ordering (i.e. NSMutableSet)
- KVC expects a number of conventional methods for these relationship types
## Adding Undo to RaiseMan
- this adds a layer between adding or removing employees which prepares the undo and
  redo stacks by modifying the NSUndoManager on the Document
```
// Document

func insertObject(employee: Employee, inEmployeesAtIndex index: Int) {
    print(" adding \( employee) to the employees array") // Add the inverse of this operation to the undo stack
    let undo: NSUndoManager = undoManager!
    undo.prepareWithInvocationTarget(self).removeObjectFromEmployeesAtIndex(employees.count)
    if !undo.undoing { undo.setActionName("Add Person") }
    employees.append(employee)
}

func removeObjectFromEmployeesAtIndex(index: Int) {
    let employee: Employee = employees[index]
    print("removing \( employee) from the employees array")
    // Add the inverse of this operation to the undo stack
    let undo: NSUndoManager = undoManager!
    undo.prepareWithInvocationTarget(self).insertObject(employee, inEmployeesAtIndex: index)
    if !undo.undoing { undo.setActionName("Remove Person") } // Remove the Employee from the array
    employees.removeAtIndex(index)
}
```
## Key-Value Observing
- method for registration on NSObject
```
func addObserver(
    observer: NSObject,
    forKeyPath keyPath: String,
    options: NSKeyValueObservingOptions,
    context: UnsafeMutablePointer<Void>
)
```
- specify key path, not just key
- options specifies information that should be passed to observer
- context indicates what level of the class hierarchy is observing the notification
- notification calls the following method on the observer
```
func observeValueForKeyPath(
    keyPath: String,
    ofObject object: AnyObject,
    change: [NSObject : AnyObject],
    context: UnsafeMutablePointer<Void>
)
```
## Using the Context Pointer Defensively
- possible to mistakenly intercept messages intended for a superclass
- use a class specific pointer as the context
- if the notification is not intended for the current subclass, it should
  be passed up the class hierarchy to the correct superclass
## Undo for Edits
```
// Document
// outside of class definition
private var KVOContext: Int = 0
// method
// MARK: - Key Value Observing
func startObservingEmployee(employee: Employee) {
    employee.addObserver(self, forKeyPath: "name", options: .Old, context: &KVOContext)
    employee.addObserver(self, forKeyPath: "raise", options: .Old, context: &KVOContext)
}
func stopObservingEmployee(employee: Employee) {
    employee.removeObserver(self, forKeyPath: "name", context: &KVOContext)
    employee.removeObserver(self, forKeyPath: "raise", context: &KVOContext)
}

// ...
var employees: [Employee] = [] {
    willSet { for employee in employees { stopObservingEmployee(employee) } }
    didSet { for employee in employees { startObservingEmployee(employee) } }
}

// ...
override func observeValueForKeyPath(
    keyPath: String,
    ofObject object: AnyObject,
    change: [NSObject : AnyObject],
    context: UnsafeMutablePointer<Void>
) {
    if context != &KVOContext {
        // If the context does not match, this message
        // must be intended for our superclass.
        super.observeValueForKeyPath(keyPath, ofObject: object, change: change, context: context)
        return
    }
    var oldValue: AnyObject? = change[NSKeyValueChangeOldKey]
    if oldValue is NSNull { oldValue = nil }
    let undo: NSUndoManager = undoManager!
    print(" oldValue =\( oldValue)")
    undo.prepareWithInvocationTarget(object).setValue(oldValue, forKeyPath: keyPath)
}
```
- need to also make Document stop observing employees on window close
- Document is defaulted to being the delegate for the Window in IB (File's Owner)
  but need to inherit from the NSWindowDelegate protocol explicitly in code
```
// MARK: - NSWindowDelegate
func windowWillClose(notification: NSNotification) { employees = [] }
```
- above implementation triggers willSet on employees property
## Begin Editing on Insert
- add table view and array controller outlets to document
- add an addEmployee action
```
@IBAction func addEmployee(sender: NSButton) {
    let windowController = windowControllers[0] as! NSWindowController
    let window = windowController.window!
    let endedEditing = window.makeFirstResponder(window)
    if !endedEditing {
        print(" Unable to end editing.")
        return
    }
    let undo: NSUndoManager = undoManager!
    // Has an edit occurred already in this event?
    if undo.groupingLevel > 0 {
        // Close the last group
        undo.endUndoGrouping()
        // Open a new group
        undo.beginUndoGrouping()
    }
    // Create the object
    let employee = arrayController.newObject() as! Employee
    // Add it to the array controller's contentArray
    arrayController.addObject(employee)
    // Re-sort (in case the user has sorted a column)
    arrayController.rearrangeObjects()
    // Get the sorted array
    let sortedEmployees = arrayController.arrangedObjects as! [Employee]
    // Find the object just added
    let row = find(sortedEmployees, employee)!
    // Begin the edit in the first column
    print("starting edit of \(employee) in row \(row)")
    tableView.editColumn(0, row: row, withEvent: nil, select: true)
}
```
- connect add button to addEmployee action and connect File's Owner outlets for
  table view and array controller
## For the More Curious: Windows and the Undo Manager
- a view can add inverse actions for edits to the undo manager
- view queries delegate for the undo manager to use
```
optional func undoManagerForTextView( view: NSTextView!) -> NSUndoManager?
```
- then queries window
```
// property on NSWindow inherited from NSResponder
var undoManager: NSUndoManager? { get }
```
- windows delegate can supply the following method
```
optional func windowWillReturnUndoManager( window: NSWindow) -> NSUndoManager?
```
- key window: active window (Cocoa term because it gets keyboard events)

# 12. Archiving
- object-oriented program is composed of complex graph of interconnected objects
- archiving is a mechanism to represent this graph as a stream of bytes
- aka serialization
- only need properties, property values, and name of class, not methods in archive
- XIB files use their own archiving and unarchiving (via XML)
- two steps -> define how to archive and indicate when to archive
## NSCoder and NSCoding
- NSCoding protocol
    - init(coder aDecoder: NSCoder)
    - encodeWithCoder(aCoder: NSCoder)
- add to Employee
- NSCoder is an abstract class
- will create NSKeyedArchiver and NSKeyedUnarchiver as concrete classes
## Encoding
- commonly use encodeObject(anObject: AnyObject?, forKey aKey: String)
    - triggers encodeWithCoder on anObject
- NSCoder has the following for common value types
```
func encodeBool(boolv: Bool, forKey key: String)
func encodeDouble(realv: Double, forKey key: String)
func encodeFloat(realv: Float, forKey key: String)
func encodeInt(intv: Int32, forKey key: String)
```
- inherit from NSCoding in Employee
```
func encodeWithCoder(aCoder: NSCoder) {
    if let name = name { aCoder.encodeObject(name, forKey: "name") }
    aCoder.encodeFloat( raise, forKey: "raise")
}
// or if superclass also implements
override func encodeWithCoder(aCoder: NSCoder) {
    super.encodeWithCoder(aCoder)
    if let name = name { aCoder.encodeObject( name, forKey: "name") }
    aCoder.encodeFloat( raise, forKey: "raise")
}
```
## Decoding
- methods for decoding common value types
```
func decodeObjectForKey(key: String) -> AnyObject?
func decodeBoolForKey(key: String) -> Bool
func decodeDoubleForKey(key: String) -> Double
func decodeFloatForKey(key: String) -> Float
func decodeIntForKey(key: String) -> Int32
```
- add following methods
```
// call super.init(coder:) if it has it
required init(coder aDecoder: NSCoder) {
    name = aDecoder.decodeObjectForKey("name") as! String?
    raise = aDecoder.decodeFloatForKey("raise")
    super.init()
}
// also add init()
```
- do not always need to inherit or add init methods in Swift
## The Document Architecture
- composed of 3 classes
    - NSDocumentController
    - NSDocument
    - NSWindowController
- handles MVC and directly or via array controller
    - save model data to file
    - load data from file
    - give view data to display
    - handle user input and update model
## Info.plist and NSDocumentController
- Info.plist contains application info
- indicates whether app is document based (and creates NSDocumentController on load if so)
- rarely interact with doc controller
```
let documentController = NSDocumentController.sharedDocumentController()
```
## NSDocument
- generally can just subclass and not interact with the rest of the doc architecture
- may need to add NSWindowController subclasses for view components
- for save, save all, and save as
```
func dataOfType(typeName: String, error outError: NSErrorPointer) -> NSData?
func fileWrapperOfType(typeName: String!, error outError: NSErrorPointer) -> NSFileWrapper?
func writeToURL(url: NSURL, ofType typeName: String, error outError: NSErrorPointer)
```
- NSErrorPointer -> errors are stored in NSErrorPointer's memory field -> must create
  error if method fails
- for open, open recent, revert to saved
```
func readFromData(data: NSData, ofType typeName: String, error outError: NSErrorPointer) -> Bool
func readFromFileWrapper(fileWrapper: NSFileWrapper, ofType typeName: String, error outError: NSErrorPointer) -> Bool
func readFromURL(url: NSURL, ofType typeName: String, error outError: NSErrorPointer) -> Bool
```
- need to call the following to set up the UI after loading
```
func windowControllerDidLoadNib(windowController: NSWindowController!)
// or for revert to saved update UI in load method by checking outlets for nil
```
-

## NSWindowController
- one instance for each window displayed (in windowControllers property)
- generally can rely on the default behavior of the single window controller
- cases for customizing
    - more than one window on same document i.e. design, CAD, DAW
    - separate UI controller and model controller logic
## Saving and NSKeyedArchiver
- saving requires an NSData object -> returning this will allow writing to file
- NSKeyedArchiver has the following method
```
class func archivedDataWithRootObject(rootObject: AnyObject) -> NSData
```
- encoding is performed through a propagating process of encoding
- to be able to save
```
override func dataOfType(typeName: String, error outError: NSErrorPointer) -> NSData? {
    // End editing
    tableView.window?.endEditingFor(nil)
    // Create an NSData object from the employees array
    return NSKeyedArchiver.archivedDataWithRootObject(employees)
}
```
- will not throw
## Loading and NSKeyedUnarchiver
- use NSKeyedUnarchiver's method
```
class func unarchiveObjectWithData( data: NSData) -> AnyObject?
```
- modify Document
```
override func readFromData(
    data: NSData,
    ofType typeName: String,
    error outError: NSErrorPointer
) -> Bool {
    print("About to read data of type \(typeName).")
    employees = NSKeyedUnarchiver.unarchiveObjectWithData(data) as! [Employee]
    return true
}
```
- NSArrayController handles UI updates but will later modify windowControllerDidLoadNib
- document specifies nib through windowNibName property
## Setting the Extension and Icon for the File Type
- for icon, add .icns file to project by dragging into Supporting Files and copying if necessary
- for doc type info, select RaiseMan in project navigator
    - Info
        - set name, Icon and identifier, and UTI under target info
        - set UTI Description, Identifier, and Extensions to settings above
        - Conforms To -> public.data
- UTI - describes the data contained in the file
- changes are saved to Info.plist
## Application Data and URLs
- shoebox app (collections of documents of type) should store data in ~/Library/Application Support
- do not build many of these default URLs manually
- for common directories, use NSFileManager's URLsForDirectory(_:inDomains:)
    - ApplicationSupportDirectory
    - CachesDirectory
    - DocumentDirectory
- for the first time, create directory using NSFileManager's createDirectoryAtURL(_:withIntermediateDirectories:attributes:error:)
## For the More Curious: Preventing Infinite Loops
- for archiving object graphs with cycles, NSKeyedArchiver creates objects with tokens
- if an object is encountered again, only its token is added to the stream
- when unarchiving, if a token is found with no data, the NSKeyedUnarchiver knows to look
  up the token in the table
- the following NSCoder method is often confusing to developers
```
func encodeConditionalObject(objv: AnyObject?, forKey key: String)
```
- used for when object A has a ref to B but B does not need to be archived
- if another object has archived B, A adds B's token to stream, otherwise treat as nil
## For the More Curious: Creating a Protocol
- define interface using protocol keyword
- tagging with @objc adds the ability to add optional methods
## For the More Curious: Automatic Document Saving
- 10.7 and after added auto saving which is triggered by a change count and allows
  browsing of versions via a Time Machine-like interface
- opt in
```
override class func autosavesInPlace() -> Bool { return true }
```
- see Mac App Programming Guide for more info
## For the More Curious: Document-Based Applications Without Undo
- NSDocument keeps track of the number of changes
```
func updateChangeCount(change: NSDocumentChangeType)
```
- has following types
    - ChangeDone - increments
    - ChangeUndone - decrements
    - ChangeCleared - zeroes
- window marked dirty when change count is not 0
## For the More Curious: Universal Type Identifiers
- answering question what a file represents is answered in a number of ways
    - file extensions
    - creator codes
    - MIME types
- Apple's long term solution is UTI
- hierarchically organized
- documents can read a set of UTIs (including custom) specified in Info.plist
- pasteboard also uses UTIs to identify data types
- see Uniform Type Identifiers Reference guide for more info

# 13. Basic Core Data
- core data handles writing data objects (i.e. generates the object definition from
  a schema), saving, loading, and undo
## Defining the Object Model
- must define the managed object model
- entities: similar to object, has attributes (value types) and relationships (refs to
  other objects)
- stored in an .xcdatamodeld file and instance of NSManagedObjectModel class
- documents subclass NSPersistentDocument to manage reading and saving
- NSManagedObjectContext manages all of the entities (create, get, delete)
  (managedObjectContext property on NSPersistentDocument)
- NSManagedObject is the base class for models
- create Xcode project
    - document based
    - ignore extension
    - use core data
- open Document.xcdatamodeld
    - Add Entity -> Car
        - condition -> Integer 16
        - datePurchased -> Date
        - makeModel -> String
        - onSpecial -> Boolean
        - photo -> Transformable
        - price -> Decimal
## Configure the Array Controller
- add array controller
- Bindings Inspector
    - Bind to -> File's Owner
    - Model Key Path -> managedObjectContext
- Attributes Inspector
    - Object Controller
        - Mode -> Entity Name
        - Entity Name -> Car
        - Prepares Content -> On
- Prepares Content to on makes the controller fetch immediately after creation
- to add a label to an object in IB
    - Identity Inspector
        - Label -> value
- set NSArrayController's label to Cars
## Add the Views
- add a table view
- Attributes Inspector
    - Columns -> 3
- Column names -> Make/Model, Price, Special
- Delete the table cell view in the first column and drop and drop an Image & Text
  Table Cell View onto it
- add a number formatter to second column
    - Style -> Currency
- remove table cell view in third column and replace with a checkbox (Check Box Cell)
- Attributes Inspector for text fields in Make/Model column and Price column
    - Editable -> X
- add add and remove buttons, NSDatePicker, NSImage (Image Wells), NSLevelIndicator,
  and 2 labels
- NSImageView
    - Attributes Inspector
        - Editable -> X
- NSLevelIndicator
    - Attributes Inspector
        - Level Indicator
            - Style -> Rating
            - State -> Editable (X)
        - Value
            - Minimum -> 0
            - Maximum -> 5
- Select date picker, image view, 2 labels, and level indicator
    - top menu
        - Editor
            - Embed In
                - Box
## Connections and Bindings
- table view
    - Content -> arrangedObjects of Car
    - Model Key Path -> ""
    - Selection Indexes -> selectionIndexes of Car
- each column's table cell view
     - First
        - Image View
            - Binding -> Value
            - Bind to -> Table Cell View
            - Controller Key -> ""
            - Key Path -> objectValue
        - Text Field
            - Binding -> Value
            - Bind to -> Table Cell View
            - Controller Key -> ""
            - Key Path -> objectValue
     - Second
        - Binding -> Value
        - Bind to -> Table Cell View
        - Controller Key -> ""
        - Key Path -> objectValue
     - Third
        - Binding -> Value
        - Bind to -> Table Cell View
        - Controller Key -> ""
        - Key Path -> objectValue
- add button -> action s/b add:
- remove button -> action s/b remove:
    - Bindings Inspector
        - Enabled -> Cars array controller
        - Controller Key -> canRemove
        - Model Key Path -> ""
- detail controls bindings
    - textual date picker with stepper
        - Binding -> Value
        - Bind to -> Cars
        - Controller Key -> selection
        - Key Path -> datePurchased
    - rating level indicator
        - Binding -> Value
        - Bind to -> Cars
        - Controller Key -> selection
        - Key Path -> condition
    - image well
        - Binding -> Value
        - Bind to -> Cars
        - Controller Key -> selection
        - Key Path -> photo
        - Conditionally Set Editable -> X
- NSBox
    - Title With Pattern
        - Display Pattern Title1 -> Cars
    - Controller Key -> selection
    - Model Key Path -> makeModel
    - Display Pattern -> Details for %{title}@
    - No Selection Placeholder -> <no selection>
    - Null Placeholder -> <no Make/Model>
- first two column text fields
    - Font Bold
        - Value -> Table Cell View
        - Controller Key -> ""
        - Model Key Path -> objectValue.onSpecial
## How Core Data Works
- NSPersistentDocument reads in the model and creates NSManagedObjectModel
- object model has 1 NSEntityDescription (describes Car)
    - has several instances of NSAttributeDescription
- doc creates instance of NSPersistentStoreCoordinator and NSManagedObjectContext
- NSManagedObjectContext fetches instances of NSManagedObject from the object store
- while objects are in memory, context observes them
    - when changed, registers undo with NSUndoManager
    - knows when objects need to be saved on change
- NSManagedObjectContext is the object most interacted with by programmers
    - fetch objects and save change to object graph
## Fetching Objects from the NSManagedObjectContext
- create NSFetchRequest (typically only used once)
    - use name of entity
    - add NSPredicate and NSSortDescriptor to manage filtering and sorting
```
// Build the fetch request:
let fetchRequest = NSFetchRequest(entityName: "Car")
fetchRequest.predicate = NSPredicate(format: "price > = 1000")
fetchRequest.sortDescriptors = [NSSortDescriptor( key: "makeModel", ascending: true)]
// Execute the fetch request:
var error: NSError?
let fetchResult: [AnyObject]? = managedObjectContext.executeFetchRequest(fetchRequest, error: &error)

// usage
if let cars = fetchResult as? [NSManagedObject] {
    for car in cars {
        if let makeModel = car.valueForKey("makeModel") as? String {
            print(" Fetched car: \( makeModel)")
        }
    }
}
```
## Persistent Store Types
- supported persistent store types
    - for saving and loading
        - SQlite
        - XML
        - Binary
    - caching
        - In-Memory
- prefer SQLite for most applications
- XML is slower
- Core Data is not a database adapter so you cannot use your own databases with it
- see Core Data Programming Guide
## Choosing a Cocoa Persistence Technology
- complex topic
- uses faulting to only load fetched objects
- handles tracking changes, helping to manage schema versions and more
- downside is that it is a black box and can be difficult to debug
- remember NSManagedObject instances are closely tied to the NSManagedObjectContext
  from which they were fetched
- archiving and core data both handle saving and loading, core data has speed over
  the need to save and load the entire object graph
- prefer core data for medium level persistence needs
## Customizing Objects Created by NSArrayController
- create CarArrayController.swift and add
```
override func newObject() -> AnyObject {
    let newObj = super.newObject() as! NSObject
    let now = NSDate() newObj.setValue(now, forKey:" datePurchased")
    return newObj
}
```
- set class of array controller to CarArrayController in Identity Inspector
## Challenge: Begin Editing on Add
- look at RaiseMan Document.addEmployee(_:)
- requires an outlet on CarArrayController
## Challenge: Implement RaiseMan Using Core Data

# 14. User Defaults
- store preferences for behavior or appearance in user's defaults database
## NSUserDefaults
- NSUserDefaults class
- can use factory defaults
- at an application level, the user modifies preferences
- NSUserDefaults works with defaults
    - register, write, and read defaults to reflect user's preferences
- uses strings for settings like keys in a dictionary
- shared defaults
```
class func standardUserDefaults() -> NSUserDefaults
// returns the shared defaults object
```
- registering defaults
```
func registerDefaults(registrationDictionary: [NSObject : AnyObject])
// registers the factory defaults for the application
```
- setting defaults
```
func setBool(value: Bool, forKey defaultName: String)
func setFloat(value: Float, forKey defaultName: String)
func setInteger(value: Int, forKey defaultName: String)
func setObject(value: AnyObject?, forKey defaultName: String)
// set the value for the defaultName default
// use the method that is correct for the type of value that is being stored
```
- removing defaults
```
func removeObjectForKey(defaultName: String)
// removes the value for the defaultName default
// the application will return to using the value given by the factory defaults or
// elsewhere
```
- reading defaults
```
func boolForKey(defaultName: String) -> Bool
func floatForKey(defaultName: String) -> Float
func integerForKey(defaultName: String) -> Int
func objectForKey(defaultName: String) -> AnyObject?
// read the value for the defaultName default
// usually this will be the user’s previously stored default
// value, or, if it has not been set, the factory default
```

## Adding User Defaults to SpeakLine
- use a PreferenceManager wrapper class
```
class PreferenceManager {
    private let userDefaults = NSUserDefaults.standardUserDefaults()
}
```
## Create Names for the Defaults
```
private let activeVoiceKey = "activeVoice"
private let activeTextKey = "activeText"
```
- use global variables to allow compiler to check typos
## Register Factory Defaults for the Preferences
- import Cocoa for NSSpeechSynthesizer
```
func registerDefaultPreferences() {
    let defaults = [
        activeVoiceKey : NSSpeechSynthesizer.defaultVoice(),
        activeTextKey : "Able was I ere I saw Elba."
    ]
    userDefaults.registerDefaults(defaults)
}

init() { registerDefaultPreferences() }
```
- add init to prevent any code from registering the value for a default before the
  factory default has been registered
- add to MainWindowController
```
let preferenceManager = PreferenceManager()
```
## Reading the Preferences
- add computed properties to preference class
    - activeVoice
    - activeText
```
var activeVoice: String? {
    get { return userDefaults.objectForKey(activeVoiceKey) as? String }
}
var activeText: String? {
    get { return userDefaults.objectForKey(activeTextKey) as? String }
}
```
## Reflecting the Preferences in the UI
- read properties from from PreferenceManager in MainWindowController on window load
```
func windowDidLoad() {
    super.windowDidLoad()
    updateButtons()
    speechSynth.delegate = self
    for voice in voices {
        print(voiceNameForIdentifier(voice)!)
    }
    let defaultVoice = preferenceManager.activeVoice! // **
    if let defaultRow = find(voices, defaultVoice) {
        let indices = NSIndexSet(index: defaultRow)
        tableView.selectRowIndexes(indices, byExtendingSelection: false)
        tableView.scrollRowToVisible(defaultRow)
    }
    textField.stringValue = preferenceManager.activeText! // **
}
```
## Writing the Preferences to User Defaults
```
var activeVoice: String? {
    set (newActiveVoice) {
        userDefaults.setObject(newActiveVoice, forKey: activeVoiceKey)
    }
    get { return userDefaults.objectForKey( activeVoiceKey) as? String }
}
var activeText: String? {
    set (newActiveText) {
        userDefaults.setObject(newActiveText, forKey: activeTextKey)
    }
    get { return userDefaults.objectForKey( activeTextKey) as? String }
}
```
## Storing the User Defaults
- in tableViewSelectionDidChange save user's selection of voice into preferences
```
preferenceManager.activeVoice = voice
```
- persist spoken text across runs
    - conform to NSTextFieldDelegate
    - connect IB text field connection menu
        - delegate -> File's Owner
    - implement controlTextDidChange(_:)
```
override func controlTextDidChange(notification: NSNotification) {
    preferenceManager.activeText = textField.stringValue
}
```
## What Can Be Stored in NSUserDefaults?
- property list objects: instances of Objective-C classes
    - NSDictionary
    - NSArray
    - NSString
    - NSNumber
- plist data structure must be composed entirely of these objects
- can store any data structure in NSUserDefaults that can be represented
  as a plist
- Swift basic types are bridged to these types so they can be stored without issues
## Precedence of Types of Defaults
- highest to lowest priority
    - CLI args
        - rarely used on mac OS in production app
    - application
        - settings in the user’s defaults database.
    - global defaults
        - system-wide defaults
    - language defaults
        - based on the user’s preferred language
    - registered defaults
        - factory defaults registered at launch
## What is the User’s Defaults Database?
- ~/Library/Preferences/*.plist (can be viewed with Xcode, binary plist files)
- uses bundle identifier as the name
- could edit on older OSes but managed by daemon in Mavericks and later
- use `defaults` command-line tool for Mavericks and later
## For the More Curious: Reading/ Writing Defaults from the Command Line
- read
```
defaults read com.apple.dt.Xcode
```
- write
```
defaults write com.apple.Xcode NSNavLastRootDirectoryForOpen /Users
```
- read global defaults
```
defaults read NSGlobalDomain
```
## For the More Curious: NSUserDefaultsController
- to bind to a value from the NSUserDefaults object, use NSUserDefaultsController
- SpeakLine's text field Bindings Inspector
    - Bind to -> Shared User Defaults Controller
    - Controller Key -> values
    - Model Key Path -> activeTextKey
## Challenge: Reset Preferences
- must update window to reflect changes after reset

# 15. Alerts and Closures
- use NSAlert to warn user
## NSAlert
- for simple alert
```
let alert = NSAlert()
alert.messageText = "Alert message."
alert.informativeText = "A more detailed description of the situation."
alert.addButtonWithTitle("Default")
alert.addButtonWithTitle("Alternative")
alert.addButtonWithTitle("Other")
let result = alert.runModal()
```
- NSAlert properties
    - configure the text that the alert will display
        - var messageText: String?
        - var informativeText: String?
    - configure the images that the alert will display (you can supply an icon but by default the alert will use the application’s icon)
        - var icon: NSImage!
        - var alertStyle: NSAlertStyle
- NSAlert methods
    - add the buttons on alert
        - func addButtonWithTitle( title: String) -> NSButton
    - present
        - func runModal
- check result
```
let result = alert.runModal()
switch result {
    case NSAlertFirstButtonReturn: // The user clicked "Default"
        break
    case NSAlertSecondButtonReturn: // The user clicked "Alternative"
        break
    case NSAlertThirdButtonReturn: // The user clicked "Other"
        break
    default:
        break
}
```
- automatically adds an OK button if no buttons are added
## Modals and Sheets
- shows modal and blocks until user dismisses the alert
- can also run as a sheet on top of other windows
    - stops events for related window
    - other windows in app will receive events
## Completion Handlers and Closures
- completion handler = callback
    - wait for user
    - network
    - calculations on a separate thread
- uses closures in Swift
```
let say = { (text: String) -> Void in print(" The closure says: \( text)"") }
say(" Hello")
```
- for alerts as sheets, configure alert and then use beginSheetModalForWindow(_:completionHandler:)
```
let alert = NSAlert() ...
let completionHandler = { (response: NSModalResponse) -> Void in print("The user selected \(response).") }
alert.beginSheetModalForWindow(window, completionHandler: completionHandler)
```
## Closures and capturing
- can capture constants and variables in enclosing scope
- captures reference objects with strong reference by default
- this can cause strong reference cycle
- solution is a capture list
```
chauffeur.completionHandler = { [unowned chauffeur] in chauffeur.sendMessage(" I have arrived at the portico.") }
```
- use keywords unowned and weak
- unowned -> captured reference should never be deallocated before the closure (similar
  to implicitly unwrapped optional)
- weak -> treats capture as optional and may be deallocated before closure
- to capture properties or methods, must use the self reference and can include in capture list
## Make the User Confirm the Deletion
- in RaiseMan open Document.xib
    - select table view Attributes Inspector
        - Selection
            - Multiple -> X
- Document -> display alert as sheet and call remove depending on selection
```
@IBAction func removeEmployees(sender: NSButton) {
    let selectedPeople: [Employee] = arrayController.selectedObjects as! [Employee]
    let alert = NSAlert()
    alert.messageText = "Do you really want to remove these people?"
    alert.informativeText = "\(selectedPeople.count) people will be removed."
    alert.addButtonWithTitle("Remove")
    alert.addButtonWithTitle("Cancel")
    let window = sender.window!
    alert.beginSheetModalForWindow(
        window,
        completionHandler: { (response) -> Void in
            // If the user chose "Remove", tell the array controller to delete the people
            switch response {
                // The array controller will delete the selected objects
                // The argument to remove() is ignored
                case NSAlertFirstButtonReturn:
                    self.arrayController.remove(nil)
                default: break
            }
        }
    )
}
```
- need to change target action on remove button
    - File's Owner -> removeEmployees:
## For the More Curious: Functional Methods and Minimizing Closure Syntax
- map, reduce, filter
- if closure is last parameter it can sit outside of parens
- if closure is only parameter, no parens are needed
- if closure is single statement, can leave off return keyword from closure
- for simple closures, can use $0, $1, etc for parameters
## Challenge: Don’t Fire Them Quite Yet
- add "Keep, but no raise" button which does not delete employee and sets raise
  to 0
## Challenge: Different Messages for Different Situations
- modify informativeText to handle various plural cases and display the name of
  the employee for one person
- modify messageText to handle various plural cases

# 16. Using Notifications
## What Notifications Are
- each application has a single instance of NSNotificationCenter
- objects register as observers and posters
- when a poster posts a notification that is forwarded to all related observers
## What Notifications Are Not
- not IPC, limited to same application
- look at NSDistributedNotification center for passing notifications
  between applications
## NSNotification
- properties
    - name: String
    - object: AnyObject?
- object is almost always the object that posted the notification
## NSNotificationCenter
- 3 main pieces of functionality:
    - register observers
    - post notifications
    - unregister observers
- commonly used methods
```
class func defaultCenter() -> NSNotificationCenter
func addObserver(observer: AnyObject, selector aSelector: Selector, name aName: String?, object anObject: AnyObject?)
func postNotification(notification: NSNotification)
func postNotificationName(aName: String, object anObject: AnyObject?)
func removeObserver(observer: AnyObject)
```
## Starting the Chatter Application
- create Xcode project as usual (uncheck Use Storyboards, Create Document-Based Application, and Use Core Data)
- remove MainMenu.xib window and outlet to window in AppDelegate.swift
- create ChatWindowController.swift and .xib
- xib Attributes Inspector
    - Visible at Launch -> `[]` (will not display until showWindow is called)
- add windowNibName
```
class ChatWindowController: NSWindowController {
    // MARK: - Lifecycle override
    var windowNibName: String { return "ChatWindowController" }
    ...
```
- in AppDelegate
```
var windowControllers: [ChatWindowController] = []

func addWindowController() {
    let windowController = ChatWindowController()
    windowController.showWindow(self)
    windowControllers.append(windowController)
}

func applicationDidFinishLaunching(aNotification: NSNotification) { addWindowController() }

@IBAction func displayNewWindow(sender: NSMenuItem) { addWindowController() }
```
- in .xib ctrl-drag from menu
    - File -> New -> App Delegate
        - action -> displayNewWindow:
- in ChatWindowController.swift
```
dynamic var log: NSAttributedString = NSAttributedString(string: "")
dynamic var message: String? // NSTextView does not support weak references.
@IBOutlet var textView: NSTextView!

@IBAction func send(sender: AnyObject) { }
```
- add text view, text field and button to .xib
- text view Bindings Inspector
    - Attributed String -> File's Owner log property
- text view Attributes Inspector
    - Editable -> `[]`
- File's Owner's textView -> text view
- text field Bindings Inspector
    - Value -> File's Owner message property
- button connections pop-up
    - target -> File's Owner
    - action -> send:
- window connections pop-up
    - initialFirstResponder -> text field (makes text field active on window open)
## Using Notifications in Chatter
- each window controller posts a notification when a user sends a message
- each window observes and when they observe a message they update text views
  to display message text
- in ChatWindowController.swift
    1. name of notification to post
    2. key in the notification's user info dictionary to store the message
```
// global constants
private let ChatWindowControllerDidSendMessageNotification = "com.bignerdranch.chatter.ChatWindowControllerDidSendMessageNotification"
private let ChatWindowControllerMessageKey = "com.bignerdranch.chatter.ChatWindowControllerMessageKey"

... in class

override func windowDidLoad() {
    super.windowDidLoad()

    let notificationCenter = NSNotificationCenter.defaultCenter()
    notificationCenter.addObserver(self, selector: Selector("receiveDidSendMessageNotification:"), name: ChatWindowControllerDidSendMessageNotification, object: nil)
}

@IBAction func send(sender: AnyObject) {
    sender.window?.endEditingFor(nil)
    if let message = message {
        let userInfo = [ChatWindowControllerMessageKey : message]
        let notificationCenter = NSNotificationCenter.defaultCenter()
        notificationCenter.postNotificationName(ChatWindowControllerDidSendMessageNotification, object: self, userInfo: userInfo)
    }
    message = ""
}

// ChatWindowControllerDidSendMessageNotification
func receiveDidSendMessageNotification(note: NSNotification) {
    let mutableLog = log.mutableCopy() as! NSMutableAttributedString
    if log.length > 0 {
        mutableLog.appendAttributedString(NSAttributedString( string: "\ n"))
    }
    let userInfo = note.userInfo! as! [String : String]
    let message = userInfo[ChatWindowControllerMessageKey]!
    let logLine = NSAttributedString(string: message)
    mutableLog.appendAttributedString(logLine)
    log = mutableLog.copy() as! NSAttributedString
    textView.scrollRangeToVisible(NSRange( location: log.length, length: 0))
}

deinit {
    let notificationCenter = NSNotificationCenter.defaultCenter()
    notificationCenter.removeObserver(self)
}
```
- add ChatWindowController to the default notification center as an observer of ChatWindowControllerDidSendMessageNotification
  whenever any object posts to it inside windowDidLoad
- send
    - check if message is non-nil
    - if not, add to user info dictionary
    - post to notification center
- handle a notification by posting text to text view and scroll to ensure display
- windows observe notifications from themselves as well
- add cleanup in deinit
## For the More Curious: Delegates and Notifications
- a delegate for an object should (most-likely) receive notifications for that
  object
- for standard Cocoa objects, the delegate is automatically registered as an
  observer for the methods it implements
- method naming convention
    - name of notification -> NS?some notification name?Notification
    - downcase first letter ?some notification name? (removing NS and Notification)
    - argument is (notification: NSNotification)
- refer to docs for each delegate type
## Challenge: Beep-beep!
- to beep when relinquishing an application's active status, use NSApplication's
  NSApplicationDidResignActiveNotification notification
- AppDelegate handles this
- NSBeep() causes a system beep
## Challenge: Add Usernames
- add text field to the top of ChatWindowController to enter a username
- add username or a good default to notifications
- display username in text window
## Challenge: Colored Text
- to distinguish messages from current window vs other windows
    - in receiveDidSendMessageNotification(_:) handle posting by self
    - add attribute to logLine to display other color with NSForegroundColorAttributeName
## Challenge: Disabling the Send Button
- should disable send button when no text has been entered
- bind send button's Enabled -> File's Owner message property
- to handle String -> Bool conversion, use subclass of NSValueTransformer (IsNoteEmptyTransformer)
- update button state as user types
    - text field's Bindings Inspector
        - Value -> Continuously Updates Value -> X

# 17. NSView and Drawing
- visible objects are windows or views (NSWindow or NSView)
- window has collection of views
    - view draws inside rectangles and handles events
- subclasses of NSView so far
    - NSButton
    - NSTextField
    - NSTableView
    - NSColorWell
- to expand functionality, subclass NSView
## Setting Up the Dice Application
- setup Xcode project (unchecking usual)
- add AppDelegate snippet
```
class AppDelegate: NSObject, NSApplicationDelegate {
    var mainWindowController: MainWindowController?
    func applicationDidFinishLaunching(aNotification: NSNotification) {
        let mainWindowController = MainWindowController()
        mainWindowController.showWindow(self)
        self.mainWindowController = mainWindowController
    }
}
```
- create MainWindowController.swift and .xib
- override windowNibName
```
class MainWindowController: NSWindowController {
    override var windowNibName: String { return "MainWindowController" }
    override func windowDidLoad() { super.windowDidLoad() }
}
```
## Creating a view subclass
- create DieView class
```
import Cocoa
class DieView: NSView { }
```
- add Custom View to .xib
- custom view Identity Inspector
    - Custom Class
        - Class -> DieView
## Views, Rectangles, and Coordinate Systems frame bounds
- to display a rectangle of a view, Cocoa uses
    - uses origin (lower left corner in relation to origin of superview)
    - size
- frame
    - NSRect
        - origin: NSPoint (lower left corner as offset from superview)
        - size: NSSize
- NSPoint
    - x: CGFloat
    - y: CGFloat
- NSSize:
    - width: CGFloat
    - height: CGFloat
- all are structs for performance and to avoid bugs as value types
- NSPoint and NSSize are measured in points (not related to NSPoint)
- point is 1/72 of an inch (from typography) because original mac used
  72 pixels per inch
- points are resolution independent (facilitates supporting both standard
  and retina displays easily)
- NSRect, NSPoint, and NSSize are type aliases for the Core Graphics types CGRect, CGPoint, and CGSize
- bounds
    - same type as frame
    - frame is used to position subviews of the view
    - bounds are used to draw the view itself
## Custom Drawing
- three steps
    - set color (using NSColor with RGB, HSV, CMYK, or grayscale)
    - construct path (NSBezierPath, can store as property)
    - draw path in color (NSBezierPath -> stroke(), shapes -> fill())
- handle steps in NSView drawRect(_:)
## drawRect(_:)
```
class DieView: NSView {
    override func drawRect(dirtyRect: NSRect) {
        let backgroundColor = NSColor.lightGrayColor()
        backgroundColor.set()
        NSBezierPath.fillRect(bounds)

        NSColor.greenColor().set()
        let path = NSBezierPath()
        path.moveToPoint(NSPoint(x: 0, y: 0))
        path.lineToPoint(NSPoint(x: bounds.width, y: bounds.height))
        path.stroke()
    }
}
```
- notice use of bounds.width and bounds.height
## When is my view drawn?
- drawRect called automatically
- don't redraw with drawRect directly, set view's needsDisplay to true
- sets view to dirty and system will call drawRect on all dirty views
- do not need to mark Cocoa's standard views as dirty because they handle
  that themselves
- mark custom views as dirty on change
## Graphics contexts and states
- Cocoa configures graphics context for a view before calling drawRect
  (held in thread local storage)
- tracks current color, font and transform
- drawing commands read and write to that state
## Drawing a die face
- in DieView
```
// for dots
var intValue: Int? = 1 { didSet { needsDisplay = true } }

func metricsForSize(size: CGSize) -> (edgeLength: CGFloat, dieFrame: CGRect) {
    let edgeLength = min(size.width, size.height)
    let padding = edgeLength / 10.0
    let drawingBounds = CGRect(x: 0, y: 0, width: edgeLength, height: edgeLength)
    let dieFrame = drawingBounds.rectByInsetting(dx: padding, dy: padding)
    return (edgeLength, dieFrame)
}

func drawDieWithSize(size: CGSize) {
    if let intValue = intValue {
        let (edgeLength, dieFrame) = metricsForSize(size)
        let cornerRadius:CGFloat = edgeLength / 5.0

        // Dot positioning
        let dotRadius = edgeLength / 12.0
        let dotFrame = dieFrame.rectByInsetting(dx: dotRadius * 2.5, dy: dotRadius * 2.5)

        // Draw the rounded shape of the die profile:
        NSColor.whiteColor().set()
        NSBezierPath(roundedRect: dieFrame, xRadius: cornerRadius, yRadius: cornerRadius).fill()

        // Dot drawing
        NSColor.blackColor().set()
        // Nested function to make drawing dots cleaner:
        func drawDot(u: CGFloat, v: CGFloat) {
            let dotOrigin = CGPoint(x: dotFrame.minX + dotFrame.width * u, y: dotFrame.minY + dotFrame.height * v)
            let dotRect = CGRect( origin: dotOrigin, size: CGSizeZero).rectByInsetting( dx: -dotRadius, dy: -dotRadius)
            NSBezierPath(ovalInRect: dotRect).fill()
        }

        // If intValue is in range...
        if find(1...6, intValue) != nil {
            // Draw the dots:
            if find([ 1, 3, 5], intValue) != nil {
                drawDot(0.5, 0.5) // Center dot
            }
            if find(2...6, intValue) != nil {
                drawDot( 0, 1) // Upper left
                drawDot( 1, 0) // Lower right
            }
            if find(4...6, intValue) != nil {
                drawDot( 1, 1) // Upper right
                drawDot( 0, 0) // Lower left
            }
            if intValue == 6 {
                drawDot( 0, 0.5) // Mid left/right
                drawDot( 1, 0.5)
            }
        }
    }
}

override func drawRect(dirtyRect: NSRect) {
    let backgroundColor = NSColor.lightGrayColor()
    backgroundColor.set()
    NSBezierPath.fillRect(bounds)
    drawDieWithSize(bounds.size)
}
```

## Saving and Restoring the Graphics State
- sometimes useful to treat graphics state like a stack, saving and restoring
  at points
- adding to drawDieWithSize causes all subsequent drawing commands to add shadow
```
let shadow = NSShadow()
shadow.shadowOffset = NSSize(width: 0, height: -1)
shadow.shadowBlurRadius = edgeLength/ 20 shadow.set()
```
- use NSGraphicsContext: saveGraphicsState() and restoreGraphicsState()
```
NSGraphicsContext.saveGraphicsState()
let shadow = NSShadow()
shadow.shadowOffset = NSSize(width: 0, height: -1)
shadow.shadowBlurRadius = edgeLength/ 20 shadow.set()
// Draw the rounded shape of the die profile:
NSColor.whiteColor().set()
NSBezierPath(roundedRect: dieFrame, xRadius: cornerRadius, yRadius: cornerRadius).fill()
NSGraphicsContext.restoreGraphicsState()
// Shadow will not apply to subsequent drawing commands
```
## Cleaning up with Auto Layout
- view instance does not currently resize
- select DieView in .xib
    - four Auto Layout buttons in bottom right of canvas
    - click pin button
        - in popover
            - Add New Constraints -> click all four struts
            - set all four text fields to 20
- if a mistake is made, click Resolve Auto Layout Issues
- a view's intrinsic content size is the natural size of its content
- Auto Layout will not reduce a view's size below its intrinsic content
  size
- in DieView
```
override var intrinsicContentSize: NSSize { return NSSize( width: 20, height: 20) }
```
## Drawing Images
- create ImageTiling Xcode project
- create TiledImageView.swift and .xib
```
import Cocoa class TiledImageView: NSView {
    var image: NSImage?
    let columnCount = 5
    let rowCount = 5

    override func drawRect(dirtyRect: NSRect) {
        super.drawRect(dirtyRect)
        if let image = image {
            for x in 0..<columnCount {
                for y in 0..<rowCount {
                    let frame = frameForImageAtLogicalX(x, y: y)
                    image.drawInRect(frame)
                }
            }
        }
    }

    func frameForImageAtLogicalX(logicalX: Int, y logicalY: Int) -> CGRect {
        let spacing = 10
        let width = 100
        let height = 100
        let x = (spacing + width) * logicalX
        let y = (spacing + height) * logicalY
        return CGRect( x: x, y: y, width: width, height: height)
    }
}
```
- add Custom View to .xib
- need to set image (could add outlet to image view and set image property
  in windowDidLoad)
## Inspectable properties and designable views
- in TiledImageView class
```
@IBInspectable var image: NSImage?
```
- in .xib, select image view
    - Attributes Inspector
        - Image -> image name (NSComputer)
- to see from Xcode add @IBDesignable to class
## Drawing images with finer control
- use the following method
```
func drawInRect(
    rect: NSRect,
    fromRect: NSRect,
    operation op: NSCompositingOperation,
    fraction delta: CGFloat
)
```
## Scroll Views
- NSScrollView is used for cases where view overflows displayable area
- overflowing view is known as document view
- displayed portion is visible rect
- in TiledImageView .xib
    - select image view
        - menu Editor -> Embed In -> Scroll View
- give view an intrinsic content size
```
override var intrinsicContentSize: NSSize {
    let furthestFrame = frameForImageAtLogicalX(columnCount-1, y: rowCount-1)
    return NSSize(width: furthestFrame.maxX, height: furthestFrame.maxY)
}
```
## Creating Views Programmatically
- create a button
```
let superview = window.contentView
let frame = NSRect(x: 10, y: 10, width: 200, height: 100)
let button = NSButton(frame: frame)
button.title = "Click me!"
superview.addSubview( button)
```
- to recreate the style of an existing view, add view in IB and go through
  properties in Attributes Inspector
- use playground for rapid iteration
## For the More Curious: Core Graphics and Quartz
- AppKit drawing APIs use Core Graphics (generally referred to as Quartz)
- generally same concepts within a C API and uses CGContextRef
- lower-level and sometimes finer control of drawing
## For the More Curious: Dirty Rects
- for time-consuming drawing, best to only redraw dirty rects
- setting needsDisplay to true redraws entire rectangle
- can use
```
let dirtyRect = NSRect(x: 0, y: 0, width: 50, height: 50)
myView.setNeedsDisplayInRect(dirtyRect)
```
## For the More Curious: Flipped Views
- AppKit views follow Cartesian coordinates by default
- flipped views
    - origin upper left
    - y increases as coordinates go down rectangle
- to use flipped views
```
override var flipped: Bool { return true }
```
## Challenge: Gradients
- pass NSBezierPath to a method on NSGradient
## Challenge: Stroke
- use a stroke to add border to NSBezierPath
## Challenge: Make DieView Configurable from Interface Builder
- use @IBDesignable to display DieView in IB

# 18. Mouse Events
## NSResponder
- all event handling methods are declared in NSResponder
- mouse events
```
func mouseDown(theEvent: NSEvent)
func rightMouseDown(theEvent: NSEvent)
func otherMouseDown(theEvent: NSEvent)
func mouseUp( theEvent: NSEvent)
func rightMouseUp(theEvent: NSEvent)
func otherMouseUp(theEvent: NSEvent)
func mouseDragged(theEvent: NSEvent)
func scrollWheel(theEvent: NSEvent)
func rightMouseDragged(theEvent: NSEvent)
func otherMouseDragged(theEvent: NSEvent)
```
## NSEvent
- interesting properties for mouse events
```
var locationInWindow: NSPoint { get }
var modifierFlags: NSEventModifierFlags { get }
var timestamp: NSTimeInterval { get }
unowned(unsafe) var window: NSWindow! { get }
var clickCount: Int { get }
var pressure: Float { get }
var deltaX: CGFloat { get }
var deltaY: CGFloat { get }
var deltaZ: CGFloat { get }
```
## Getting Mouse Events
- in DieView
```
override func mouseDown(theEvent: NSEvent) {
    print("mouseDown")
}
override func mouseDragged(theEvent: NSEvent) {
    print("mouseDragged location: \(theEvent.locationInWindow)")
}
override func mouseUp(theEvent: NSEvent) {
    print("mouseUp clickCount: \(theEvent.clickCount)")
}
```
## Click to Roll
- randomize side to show
```
func randomize() { intValue = Int( arc4random_uniform( 5)) + 1 }
```
- check for double-click
```
override func mouseUp(theEvent: NSEvent) {
    print("mouseUp clickCount: \(theEvent.clickCount)")
    if theEvent.clickCount = = 2 { randomize() }
}
```
- add interaction for mouseDown and mouseUp
```
var pressed: Bool = false { didSet { needsDisplay = true } }
// in mouseDown set pressed to true
// in mouseUp set pressed to false

// in metrics for size change dieFrame
var dieFrame = drawingBounds.rectByInsetting(dx: padding, dy: padding)
if pressed { dieFrame = dieFrame.rectByOffsetting(dx: 0, dy: -edgeLength/ 40) }

// in drawDieWithSize
shadow.shadowBlurRadius = (pressed ? edgeLength/ 100 : edgeLength/ 20)
```
## Improving Hit Detection
- can check hit detection with CGRect contains(_:) and metricsForSize(:)
  to get rectangle
- NSEvent provides locationInWindow
- to convert locationInWindow, NSView has
```
func convertPoint(aPoint: NSPoint, fromView aView: NSView?) -> NSPoint
func convertPoint(aPoint: NSPoint, toView aView: NSView?) -> NSPoint
```
- can call with nil
- update mouseDown
```
let dieFrame = metricsForSize(bounds.size).dieFrame
let pointInView = convertPoint(theEvent.locationInWindow, fromView: nil)
pressed = dieFrame.contains(pointInView)
```
## Gesture Recognizers
- similar to UIKit's gesture recognizer on iOS
- main gesture recognizers
    - NSClickGestureRecognizer
    - NSPanGestureRecognizer
    - NSMagnificationGestureRecognizer
    - NSPressGestureRecognizer
    - NSRotationGestureRecognizer
- provide much more fine grained and responsive control over gestures and
  mouse events
- see section for code examples
## Challenge: NSBezierPath-based Hit Testing
- NSBezierPath has a containsPoint method that will make the hit testing more
  accurate
## Challenge: A Drawing App
- use init(ovalInRect: NSRect) -> NSBezierPath to allow user to draw ovals in arbitrary locations
- add save and read
- add undo

# 19. Keyboard Events
- typing event -> window manager -> active application -> key window -> active view
- active view is determined by firstResponder property on a view
- when user changes active view, views check whether they can accept firstResponder
  status
    - NO means it will not accept first responder status
    - current first responder then checked to resign first responder status
## NSResponder
```
var acceptsFirstResponder: Bool { get }
func resignFirstResponder() -> Bool
func becomeFirstResponder() -> Bool
func keyDown(theEvent: NSEvent)
func keyUp(theEvent: NSEvent)
func flagsChanged(theEvent: NSEvent)
```
## NSEvent
- common keyboard event properties
```
var characters: String! { get }
var ARepeat: Bool { get }
var keyCode: UInt16 { get }
var modifierFlags: NSEventModifierFlags { get }
```
## Adding Keyboard Input to DieView
## Accept first responder
- set accepts first responder
```
override var acceptsFirstResponder: Bool { return true }
override func becomeFirstResponder() -> Bool { return true }
override func resignFirstResponder() -> Bool { return true }
```
- cannot receive keyboard events without accepting first responder
## Receive keyboard events
- do not need to implement keyDown and interpret characters manually
- use interpretKeyEvents which calls insertText
```
override func keyDown(theEvent: NSEvent) { interpretKeyEvents([theEvent]) }
override func insertText(insertString: AnyObject) {
    let text = insertString as! String
    if let number = text.toInt() { intValue = number }
}
```
## Putting the dice in Dice
- remove constraints
    - Editor -> Resolve Auto Layout Issues -> Clear Constraints
- resize die view to 104x104 in Size Inspector
- duplicate die view twice
- change title of window to Dice
## Focus Rings
- focus = first responder
- generally indicated with blue ring
- for table views -> selection highlight may change from gray to blue
```
override func drawFocusRingMask() { NSBezierPath.fillRect(bounds) }
override var focusRingMaskBounds: NSRect { return bounds }
```
- can draw any path with drawFocusRingMask
## The Key View Loop
- sequence of first responder-accepting views in each window
- Tab or Shift-Tab cycles them
- can manually modify with nextKeyView and initialFirstResponder
- to handle tab events add insertTab and inserBacktab
```
override func insertTab(sender: AnyObject?) { window?.selectNextKeyView(sender) }
override func insertBacktab(sender: AnyObject?) { window?.selectPreviousKeyView(sender) }
```
- cmd-W should close the window
## For the More Curious: Rollovers
- use mouseMoved, mouseEntered, mouseExited
- set acceptsMouseMovedEvents to true
- for rollovers, generally use mouseEntered and mouseExited
- when a view is added to a view's hierarchy, viewDidMoveToWindow is called
    - MouseEnteredAndExited -> mouseEntered(_:) and mouseExited(:) will be called on the owner of the
      tracking area
    - ActiveAlways -> the area will be active regardless of first-responder status
    - InVisibleRect -> tells the tracking area to ignore the rect parameter
      and automatically match the view’s visible rect as the tracking area
      bounds
- see section for code examples

## 20. Drawing Text with Attributes
## NSFont
- two types of methods
    - class methods for getting the font you want
    - methods for getting metrics on the font, such as letter height
```
class func systemFontOfSize(fontSize: CGFloat) -> NSFont!
class func boldSystemFontOfSize(fontSize: CGFloat) -> NSFont!
class func labelFontOfSize(fontSize: CGFloat) -> NSFont!
class func userFontOfSize(fontSize: CGFloat) -> NSFont!
class func userFixedPitchFontOfSize(fontSize: fontSize: CGFloat) -> NSFont!
class func titleBarFontOfSize(fontSize: CGFloat) -> NSFont!
class func messageFontOfSize(fontSize: CGFloat) -> NSFont!
class func toolTipsFontOfSize(fontSize: CGFloat) -> NSFont!

init(name fontName: String!, size fontSize: CGFloat) -> NSFont
```
- recommended to use previous methods over init to maintain consistency with
  the system
## NSAttributedString
- used to set attributes for a range of a string
- NSRange
    - location: Int
    - length: Int
- can use NSMakeRange() but faster to use range expression initializer NSRange(0...4)
- Cocoa has NSAttributedString and NSMutableAttributedString
```
let attrStr = NSMutableAttributedString(string: "Big Nerd Ranch")
attrStr.addAttribute(
    NSFontAttributeName,
    value: NSFont.boldSystemFontOfSize(22),
    range: NSRange(0...2)
)
attrStr.addAttribute(
    NSForegroundColorAttributeName,
    value: NSColor.orangeColor(),
    range: NSRange(4...7)
)
attrStr.addAttribute(
    NSUnderlineStyleAttributeName,
    value: 1,
    range: NSRange(9...13)
)
```
- global vars for most commonly used attributes
    - NSFontAttributeName - font object - 12-point Helvetica
    - ForegroundColorAttributeName - color - Black
    - ParagraphStyleAttributeName - NSParagraphStyle object - Standard paragraph style
    - UnderlineColorAttributeName - color - same as the foreground
    - UnderlineStyleAttributeName - number - 0
    - SuperscriptAttributeName - number - 0 (which means no superscripting or subscripting)
    - ShadowAttributeName - NSShadow object
- easiest way to create is from a file
- can read
    - string: typically from a plain-text file
    - RTF: Rich Text Format is a standard for text with multiple fonts and colors
           read and set the contents of the attributed string with an instance of NSData
    - RTFD: RTF with attachments and images
    - HTML: attributed string can do basic HTML layout
            use WebView for best quality
    - Word: attributed string can read and write simple .doc files
    - OpenOffice: attributed string can read and write simple .odt files
- dictionary can contain extra data for attributed string or supply nil
```
let rtfUrl = ... // Obtain an NSURL
var error: NSError?
let rtfData = NSData.dataWithContentsOfURL(
    rtfUrl,
    options: NSDataReadingOptions.DataReadingMappedIfSafe, error: &error) // -> NSData!
)
if rtfData != nil {
    var docAttrs: NSDictionary?
    let rtfAttrStr = NSAttributedString(
        data: rtfData,
        options: nil,
        documentAttributes: &docAttrs,
        error: &error
    )
    button.attributedTitle = rtfAttrStr
}
```
## Drawing Strings and Attributed Strings
- drawing NSAttributedString onto views
```
func drawAtPoint(point: NSPoint)
func drawInRect(rect: NSRect)
var size: NSSize { get }
```
- drawing NSString onto views
```
func drawAtPoint(point: NSPoint, withAttributes attrs: [NSObject : AnyObject]!)
func drawInRect(rect: NSRect, withAttributes attrs: [NSObject : AnyObject]!)
func sizeWithAttributes(attrs: [NSObject : AnyObject]!) -> NSSize
```
## Drawing Text Die Faces
- in DieView drawDieWithSize
```
...
    if intValue == 6 {
        drawDot(0, 0.5) // Mid left/right
        drawDot(1, 0.5)
    }
} else {
    var paraStyle = NSParagraphStyle.defaultParagraphStyle().mutableCopy() as! NSMutableParagraphStyle
    paraStyle.alignment = .CenterTextAlignment
    let font = NSFont.systemFontOfSize(edgeLength * 0.5)
    let attrs = [NSForegroundColorAttributeName: NSColor.blackColor(), NSFontAttributeName: font, NSParagraphStyleAttributeName: paraStyle]
    let string = "\(intValue)" as NSString string.drawInRect(dieFrame, withAttributes: attrs)
}
```
- options to center
    1. inline in the method where it is needed -> works but clutters code
    2. add a method to the class to find the correct rect ->
       enables encapsulation, options for reuse are limited to instances of this class
    3. create new method on NSString that draws the centered string ->
       best choice... enables code reuse (use extensions)
## Extensions
- cannot add stored properties on extensions
- can add computed properties but cannot add new storage to a class using them
- in NSString+Drawing.swift
```
import Cocoa

extension NSString {
    func drawCenteredInRect(rect: NSRect, attributes: [NSString: AnyObject]!) {
        let stringSize = sizeWithAttributes(attributes)
        let point = NSPoint(x: rect.origin.x + (rect.width - stringSize.width) / 2.0, y: rect.origin.y + (rect.height - stringSize.height)/ 2.0)
        drawAtPoint(point, withAttributes: attributes)
    }
}
```
- use in DieView
```
let string = "\(intValue)" as NSString
string.drawCenteredInRect(dieFrame, attributes: attrs)
```
- common naming convention for extension files is ?ClassName?+?descr of extension?.swift
## Getting Your View to Generate PDF Data
- AppKit provides methods for drawing views to PDFs
```
func dataWithPDFInsideRect( rect: NSRect) -> NSData
```
- in DieView
```
@IBAction func savePDF(sender: AnyObject!) {
    let savePanel = NSSavePanel()
    savePanel.allowedFileTypes = ["pdf"]
    savePanel.beginSheetModalForWindow(
        window!,
        completionHandler: { [unowned savePanel] (result) in
            if result == NSModalResponseOK {
                let data = self.dataWithPDFInsideRect(self.bounds)
                var error: NSError?
                let ok = data.writeToURL(
                    savePanel.URL!,
                    options: NSDataWritingOptions.DataWritingAtomic,
                    error: &error
                )
                if !ok {
                    let alert = NSAlert(error: error!)
                    alert.runModal()
                }
            }
        }
    )
}
```
- in .xib, add new menu item from object library to the File menu
- relabel to Save To PDF...
- ctrl-drag from new menu item to First Responder
    - connect to savePDF: method
## For the More Curious: NSFontManager
- NSFontManager can be used to customize fonts
```
let fontManager = NSFontManager.sharedFontManager()
let boldFont = fontManager.convertFont(someFont, toHaveTrait: NSFontTraitMask.BoldFontMask)
```
## Challenge: Color Text as SpeakLine Speaks It
- from NSSpeechSynthesizerDelegate, add
```
optional func speechSynthesizer(sender: NSSpeechSynthesizer, willSpeakWord characterRange: NSRange, ofString string: String!)
```
- use the range and NSAttributedString to change colors of words
```
let range: NSRange = ...
let theString: String = ...
let attributedString = NSMutableAttributedString(string: theString)
attributedString.addAttribute(NSForegroundColorAttributeName, value:NSColor.greenColor(), range: characterRange)
```
- read and write text field's attributedStringValue property (from NSControl)
- enable and disable text field during speaking
```
func updateTextField() {
    if speechSynth.speaking { textField.enabled = false }
    else { textField.enabled = true }
}
```

# 21. Pasteboards and Nil-Targeted Actions
## NSPasteboard
## Add Cut, Copy, and Paste to Dice
## Nil-Targeted Actions
## Looking at the XIB file
## Menu Item Validation
## For the More Curious: Which Object Sends the Action Message?
## For the More Curious: UTIs and the Pasteboard Custom UTIs
## For the More Curious: Lazy Copying
## Challenge: Write Multiple Representations
## Challenge: Menu Item

# 22. Drag-and-Drop Make DieView a Drag Source
## Starting a drag
## After the drop
## Make DieView a Drag Destination
## registerForDraggedTypes(_:)
## Add highlighting
## Implement the dragging destination methods
## For the More Curious: Operation Mask

# 23. NSTimer
## NSTimer-based Animation
## How Timers Work
## NSTimer and Strong/Weak References
## For the More Curious: NSRunLoop

# 24. Sheets
## Adding a Sheet
## Create the Window Controller
## Set Up the Menu Item
## Lay Out the Interface
## Configuring the Die Views
## Present the Sheet
## Modal Windows
## Encapsulating Presentation APIs
## Challenge: Encapsulate Sheet Presentation
## Challenge: Add Menu Item Validation

# 25. Auto Layout
## What is Auto Layout?
## Adding Constraints to RaiseMan
## Constraints from subview to superview
## Constraints between siblings
## Size constraints
## Intrinsic Content Size
## Creating Layout Constraints Programmatically
## Visual Format Language
## Does Not Compute, Part 1: Unsatisfiable Constraints
## Does Not Compute, Part 2: Ambiguous Layout
## For the More Curious: Autoresizing Masks
## Challenge: Add Vertical Constraints
## Challenge: Add Constraints Programmatically

# 26. Localization and Bundles
## Different Mechanisms for Localization
## Localizing a XIB File
## Localizing String Literals
## Demystifying NSLocalizedString and genstrings
## Explicit Ordering of Tokens in Format Strings
## NSBundle
## NSBundle’s role in localization
## Loading code from bundles
## For the More Curious: Localization and Plurality
## Challenge: Localizing the Default Name for a Newly Added Employee
## Challenge: Localizing the Undo Action Names

# 27. Printing
## Dealing with Pagination
## Adding Printing to RaiseMan
## For the More Curious: Are You Drawing to the Screen?
## Challenge: Add Page Numbers
## Challenge: Persist Page Setup

# 28. Web Services
## Web Services
## APIs
## RanchForecast Project
## NSURLSession and asynchronous API design
## NSURLSession, HTTP status codes, and errors
## Add JSON parsing to ScheduleFetcher
## Lay out the interface Opening URLs
## Safely Working with Untyped Data Structures
## For the More Curious: Parsing XML
## Challenge: Improve Error Handling
## Challenge: Add a Spinner
## Challenge: Parse the XML Courses Feed

# 29. Unit Testing
## Testing in Xcode
## Your First Test
## A Note on Literals in Testing
## Creating a Consistent Testing Environment
## Sharing Constants
## Refactoring for Testing
## For the More Curious: Access Modifiers
## For the More Curious: Asynchronous Testing
## Challenge: Make Course Implement Equatable
## Challenge: Improve Test Coverage of Web Service Responses
## Challenge: Test Invalid JSON Dictionary

# 30. View Controllers
## NSViewController
## Starting the ViewControl Application
## Windows, Controllers, and Memory Management
## Container View Controllers
## Add a Tab View Controller
## View Controllers vs. Window Controllers
## Considerations for OSX 10.9 and Earlier
## Challenge: SpeakLineViewController
## Challenge: Programmatic View Controller
## Challenge: Add a Window Controller

# 31. View Swapping and Custom Container View Controllers
## View Swapping NerdTabViewController
## Adding Tab Images
## Challenge: Boxless NerdTabViewController
## Challenge: NerdSplitViewController
## Challenge: Draggable Divider

# 32. Storyboards
## A New UI for RanchForecast
## Adding the course list
## Adding the web view
## Connecting the Course List Selection with the Web View
## Creating the CourseListViewControllerDelegate
## Creating the parent view controller
## For the More Curious: How is the Storyboard Loaded?

# 33. Core Animation
## CALayer
## Scattered Implicit Animation and Actions
## More on CALayer
## Challenge: Show Filenames
## Challenge: Reposition Image Layers

# 34. Concurrency
## Multithreading
## A Deep Chasm Opens Before You
## Improving Scattered: Time Profiling in Instruments
## Introducing Instruments
## Analyzing output from Instruments
## NSOperationQueue
## Multithreaded Scattered
## Thread synchronization
## For the More Curious: Faster Scattered
## Challenge: An Even Better Scattered

# 35. NSTask
## ZIPspector
## Asynchronous Reads
## iPing Challenge: .tar and .tgz Files

# 36. Distributing Your App
## Build Configurations
## Preprocessor Directives: Using Build Configurations to Change Behavior
## Creating a Release Build
## A Few Words on Installers
## App Sandbox
## Entitlements
## Containers
## Mediated file access and Powerbox
## The Mac App Store Receipt Validation
## Local receipt verification
## Server-based verification


