
# The textInput binding vs The value binding

#### Immediate updates

- The textInput binding updates your model immediately on each keystroke or other text entry mechanism (such as cutting or dragging text, which don’t necessarily raise any focus change events).

- The value binding, by default, only updates your model when the user moves focus out of the text box.

#### Browser event quirks handling

- The textInput binding is specifically designed to handle a wide range of browser quirks, to provide consistent and immediate model updates even in response to unusual text entry methods.

- Browsers are highly inconsistent in the events that fire in response to unusual text entry mechanisms such as cutting, dragging, or accepting autocomplete suggestions. The value binding, even with extra options such as valueUpdate: afterkeydown to get updates on particular events, does not cover all text entry scenarios on all browsers.

### Sources

[Text Input Binding](http://knockoutjs.com/documentation/textinput-binding.html)
