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
## NSCoder and NSCoding
## Encoding Decoding
## The Document Architecture
## Info.plist and NSDocumentController
## NSDocument
## NSWindowController
## Saving and NSKeyedArchiver
## Loading and NSKeyedUnarchiver
## Setting the Extension and Icon for the File Type
## Application Data and URLs
## For the More Curious: Preventing Infinite Loops
## For the More Curious: Creating a Protocol
## For the More Curious: Automatic Document Saving
## For the More Curious: Document-Based Applications Without Undo
## For the More Curious: Universal Type Identifiers

# 13. Basic Core Data
## Defining the Object Model
## Configure the Array Controller
## Add the Views Connections and Bindings
## How Core Data Works
## Fetching Objects from the NSManagedObjectContext
## Persistent Store Types
## Choosing a Cocoa Persistence Technology
## Customizing Objects Created by NSArrayController
## Challenge: Begin Editing on Add
## Challenge: Implement RaiseMan Using Core Data

# 14. User Defaults
## NSUserDefaults
## Adding User Defaults to SpeakLine
## Create Names for the Defaults
## Register Factory Defaults for the Preferences
## Reading the Preferences
## Reflecting the Preferences in the UI
## Writing the Preferences to User Defaults
## Storing the User Defaults
## What Can Be Stored in NSUserDefaults?
## Precedence of Types of Defaults
## What is the User’s Defaults Database?
## For the More Curious: Reading/ Writing Defaults from the Command Line
## For the More Curious: NSUserDefaultsController
## Challenge: Reset Preferences

# 15. Alerts and Closures
## NSAlert
## Modals and Sheets
## Completion Handlers and Closures
## Closures and capturing
## Make the User Confirm the Deletion
## For the More Curious: Functional Methods and Minimizing Closure ## Syntax
## Challenge: Don’t Fire Them Quite Yet
## Challenge: Different Messages for Different Situations

# 16. Using Notifications
## What Notifications Are
## What Notifications Are Not
## NSNotification
## NSNotificationCenter
## Starting the Chatter Application
## Using Notifications in Chatter
## For the More Curious: Delegates and Notifications Challenge: ## Beep-beep!
## Challenge: Add Usernames
## Challenge: Colored Text Challenge: Disabling the Send Button

# 17. NSView and Drawing
## Setting Up the Dice Application
## Creating a view subclass
## Views, Rectangles, and Coordinate Systems frame bounds
## Custom Drawing drawRect(_:)
## When is my view drawn?
## Graphics contexts and states
## Drawing a die face
## Saving and Restoring the Graphics State
## Cleaning up with Auto Layout
## Drawing Images
## Inspectable properties and designable views
## Drawing images with finer control
## Scroll Views
## Creating Views Programmatically
## For the More Curious: Core Graphics and Quartz
## For the More Curious: Dirty Rects
## For the More Curious: Flipped Views
## Challenge: Gradients
## Challenge: Stroke
## Challenge: Make DieView Configurable from Interface Builder

# 18. Mouse Events
## NSResponder
## NSEvent
## Getting Mouse Events
## Click to Roll
## Improving Hit Detection
## Gesture Recognizers
## Challenge: NSBezierPath-based Hit Testing
## Challenge: A Drawing App

# 19. Keyboard Events
## NSResponder
## NSEvent
## Adding Keyboard Input to DieView
## Accept first responder
## Receive keyboard events
## Putting the dice in Dice
## Focus Rings
## The Key View Loop
## For the More Curious: Rollovers

## 20. Drawing Text with Attributes
## NSFont
## NSAttributedString
## Drawing Strings and Attributed Strings
## Drawing Text Die Faces
## Extensions
## Getting Your View to Generate PDF Data
## For the More Curious: NSFontManager
## Challenge: Color Text as SpeakLine Speaks It

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

