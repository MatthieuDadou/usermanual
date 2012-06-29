Title: Keyboard Navigation



Keyboard navigation plays an important role in the usability and accessibility of your application.  Depending on your requirements, Aria Templates provide different means to handle keyboard interactions easily.

## Tab Navigation

Using the tab key to navigate through the list of controls in your interface is the most basic keyboard navigation that is natively available in any browser.  Control of this behavior can be achieved using the <code>tabindex</code> property of elements that defines the order in which they should receive focus.  This property being also supported by AT widgets, it is very intuitive to indicate the tabbing sequence for the controls of a template.
<syntaxhighlight lang="AT">
<div style="float:left; margin-right:10px">
    <input type="text" tabindex=1 />
    {@aria:Button {
        label : "First",
        tabIndex: 3,
        block: true
    } /}        
</div>

<div style="float:left">
    <input type="text" tabindex=2 />
    {@aria:Button {
        label : "Second",
        tabIndex: 4,
        block: true
    } /}        
</div>
</syntaxhighlight>

<sample sample="templates/keyboardnavigation/tabnavigation" />

Because it is possible in the same application to use templates in which the same <code>tabIndex</code> is used for different widgets, or simply because the same template may be used several times in the same panel, collisions may occur.  To avoid this issue, the Template widget provides a <code>[baseTabIndex](http://ariatemplates.com/api/#aria.widgets.CfgBeans:TemplateCfg)</code> property that sets the base index from which all of its widgets' <code>tabIndex</code> should be computed.

Consider the following code:
<syntaxhighlight lang="AT">
{@aria:Template {
    block:true,
    baseTabIndex:100,
    defaultTemplate:'myapp.modules.booking.view.Search'
}/}
{@aria:Template {
    block:true,
    baseTabIndex:200,
    defaultTemplate:'myapp.modules.booking.view.Results'
}/}
</syntaxhighlight>

In this example, each <code>tabindex</code> used by widgets in <code>Search.tpl</code> will be incremented by 100, and those in <code>Results.tpl</code> by 200.  Thus, if the following button is defined in <code>Search.tpl</code>...
<syntaxhighlight lang="AT">
{@aria:Button {
    label : "Search",
    tabIndex: 4,
    onclick: "search"
} /}        
</syntaxhighlight>
...its real <code>tabindex</code> in the resulting page will be 104.

Keep in mind the following when using <code>baseTabIndex</code>:
* It will only affect the <code>tabindex</code> property of widgets and will not have any effect on regular HTML elements.
* It is not applied recursively: if a template sets a <code>baseTabIndex</code> to 100 and one of its child templates sets it to 200, widgets in the child template will use 200 as a base for their <code>tabIndex</code>, not 300.

<sample sample="templates/keyboardnavigation/subTemplate" />

## Keymaps

Keymaps are mechanisms that AT uses to catch sets of keystrokes and assign them to specific actions.  They can be created to handle keyboard shortcuts inside a specific section or at application level.

### Creating keymaps

The [keymap object](http://ariatemplates.com/api/#aria.templates.CfgBeans:KeyMapConfiguration) defines a keystroke and which method it should execute.  It has the following properties:
* *<code>key</code>*: a key (see below)
* *<code>shift</code>*: _ (optional)_ a boolean indicating whether or not the Shift key must be pressed
* *<code>ctrl</code>*: _ (optional)_ a boolean indicating whether or not the Ctrl key must be pressed
* *<code>alt</code>*: _ (optional)_ a boolean indicating whether or not the Alt key must be pressed
* *<code>callback</code>*: a callback object as defined in [this article](Working_in_an_Asynchronous_World#Aria_Templates_Callback_Object)

The <code>key</code> parameter can either be:
* A key code, e.g. <code>120</code>
* An [aria.DomEvent](http://ariatemplates.com/api/#aria.DomEvent) code constant, e.g. <code>aria.DomEvent.KC_F9</code>
* The <code>"*"</code> wildcard character (see [using keymaps at section level](Keyboard_Navigation#At_Section_Level))
* A key shortcut string, e.g. <code>"F9"</code>
<pre style="height:146px;overflow:auto;width:150px;">
backspace
tab
num_center
enter
return
shift
ctrl
control
alt
pause
caps_lock
escape
space
pageup
page_up
pagedown
page_down
end
home
left
arrow_left
up
arrow_up
right
arrow_right
down
arrow_down
print_screen
insert
delete
zero
one
two
three
four
five
six
seven
eight
nine
a
b
c
d
e
f
g
h
i
j
k
l
m
n
o
p
q
r
s
t
u
v
w
x
y
z
context_menu
num_zero
num_one
num_two
num_three
num_four
num_five
num_six
num_seven
num_eight
num_nine
multiply
plus
minus
period
division
divide
f1
f2
f3
f4
f5
f6
f7
f8
f9
f10
f11
f12
</pre>

Example:

<syntaxhighlight lang="AT">
{
    key : "F4",
    shift : true,
    callback : {
        fn : "destroyTheWorld",
        scope : this
    }
}
</syntaxhighlight>

### Using keymaps

There are two ways to assign keymaps to your app depending on the scope you want your shortcuts to have.

#### At Section Level

Keymaps assigned to a section will only be active when the focus is inside this section.  To do so, you need to supply an array of keymaps objects to the <code>keyMap</code> property of your [section](http://ariatemplates.com/api/#aria.templates.CfgBeans:SectionCfg), like in this example:

<sample sample="templates/keyboardnavigation/sectionkeyMap" />

The return value of a section keymap's callback method defines whether or not the keyboard event should bubble or not.  If the method returns <code>true</code>, the event will be propagated to parent sections and globally defined keymaps hooks (see below), if it returns <code>false</code> the shortcut will only be interpreted by the current section.

<sample sample="templates/keyboardnavigation/bubbleKeyMap" />

We've seen earlier that it was possible to use the <code>"*"</code> wildcard character in a keymap definition.  In this case, the keymap will match any key (with specified modifiers if any) that was not previously caught by the other definition for the same section.  Because this happens before the keyboard event is passed to the parent section, a wildcard keymap can be used to trap shortcuts defined at a higher level inside a subsection.

<sample sample="templates/keyboardnavigation/wildcardKeyMap" />

#### At Application Level

Keymaps assigned at application level will be triggered wherever the focus is.  In this case, they must be set using the <code>addGlobalKeyMap()</code> method of <code>[aria.templates.NavigationManager](http://ariatemplates.com/api/#aria.templates.NavigationManager:addGlobalKeyMap:method)</code>.  This can be done anywhere in your code.

<sample sample="templates/keyboardnavigation/applicationLevelKeyMap" />

Note that, if you can provide multiple keymap in a section configuration, <code>addGlobalKeyMap()</code> only accepts one definition per call, meaning you need to use it for each keyboard shortcut you want to set.

It is also possible to delete a globally assigned keymap using the <code>removeGlobalKeyMap()</code> method to which you pass a simpler keymap definition without callback, as illustrated below:

<syntaxhighlight lang="AT">
    aria.templates.NavigationManager.removeGlobalKeyMap({
	key : "F4"
    });
</syntaxhighlight>

<span style="color:#D13838">Be careful!</span>  Hooks set using <code>addGlobalKeyMap()</code> are not automatically removed when the object that created them is destroyed.  Don't forget to remove them.

## Table-Like Navigation

The table-like navigation feature of Aria Templates allows moving focus using arrow keys inside a section.  When set, using the <code>up</code> and <code>down</code> arrow keys (along with modifiers if activated) moves the focus from one element to the next one.  Note that in this context "next one" has to be understood as "the next element defined in the template": the <code>tabIndex</code> widgets property (explained at the beginning of this article) has no effect in this case.

To enable table-like navigation for a section, you can simply set its <code>tableNav</code> property to <code>true</code> or to a <code>[TableNavConfiguration](http://ariatemplates.com/api/#aria.templates.CfgBeans:TableNavConfiguration)</code> object (a keymap definition without callback) to trigger it only when the specified modifiers are used.  The following example illustrates the functionality in a section where you can move from one textfield to another using <code>ctrl</code> + arrows keys.

<syntaxhighlight lang="AT">
     {section {
         "id" : "mySection",
         "tableNav" : {
            ctrl:true
         }
     }}
</syntaxhighlight>

<sample sample="templates/keyboardnavigation/tableNavigation" />

In tables defined within sections that enable <code>tableNav</code>, focus can be moved from cell to cell using the <code>left</code> and <code>right</code> arrows on top of <code>up</code> and <code>down</code>.  This applies to cells that contain at least a focusable element and if more than one are present focus will be set on the first one.

<sample sample="templates/keyboardnavigation/tableNavigationFocus" />

There are few things to note when using table-like navigation:
* Subsections will inherit the behavior of their parent section.
* A consequence of this is that when a parent section has enabled arrows only navigation (<code>tableNav:true</code>) all of its subsections will inherit it, even if they redefine the property to use modifiers.
* Subsections can set their <code>tableNav</code> property to <code>false</code> to prevent inheritance.
* In a section using table-like navigation, focus always moves to the next (or previous) element, including elements outside of the section where <code>tableNav</code> might not be enabled.

<sample sample="templates/keyboardnavigation/tableNavigationInherit" />