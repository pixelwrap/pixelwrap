System: apart from type all attributes are in camel case
Help me create documentation for third parties to use when generate yaml files to create ui using my php package. Here is the rules

Basic sizes: [none ,smallest, smaller, small ,big ,bigger ,biggest]
Modifiers: [x, y, top, right,bottom, left]

When describing attributes one uses the sizes, sides and axis. for example to set small border  we say border: small, if only the top side we say border: top-small, if only y axis then border: y-small 
Same for gap, border and padding. Some attributes like button size don't have dimensions and just all the basic sizes. 

Action Property:
A link that is generated and attached to form, typeahead endpoint or button.

Attributes for Action:
- label: The label for the action (e.g., “Submit”, “Delete”, “Edit”). Required when rendered inside a table
- size: Defines the size of the action element. Used when rendered inside a table.
- link: The URL or endpoint that the action should lead to when triggered. It can contain dynamic parameters. When relative the current url is used to generate the base uri.
- params: A list of parameters that need to be passed along with the action. This typically maps dynamic data (like user ID, name, etc.) to a URL. Can also include hardcoded query params
Note: The link can include placeholders for elements to be swapped directly. For params its formated as (key, value) or as object with (key and value properties). value can be the dynamic property in plain text, no fancy brackets or parenthesis

Use Case Scenarios for an action:
- Form Submission: When a user clicks a submit button, an action can be defined to send form data to a URL.
- TypeAhead: Url for typeahead searching endpoint.
- Link Navigation: A link component might have an action that directs the user to a different page or resource when clicked.
- Table actions: eg Delete or Edit a record:

Component and their Attributes and Options:

Heading Component:
 - Size  Defines the labels’s size, Default small
 - Label 

Button Component:
 - Role: Defines the function of the button (link,reset,button,submit). When link is set an action must be set also. Default is submit
 - Variant: Defines the button’s appearance or theme (primary, secondary, success, danger). Default is primary
 - Size: Defines the button’s size (refer to the above Size Options). Default small.
 - Action as Defined

Input Component:
- id: required, Field id, also used a form field name.
- fieldType: Defines the input type (e.g., text, email, password, number). Default is text.
- required: Marks the input as required (true, false). Default false
- placeholder: Placeholder text shown in the input field.
- label.
- showLabel: Option to show or hide the label (true, false). Default true

Form Component:
- Method: Defines the HTTP method used by the form (get, post, etc.). Required.
- Action: Defines the form submission URL. Required
- Nodes (Others components like inputs), Required

Table Component:
- Dataset: Defines the data source for the table. Required
- highlight: Defines the column to display in bold.
- Indexed: Defines if the table has row numbers, Default true.
- Fields: Defines the columns of the table with keys like name, email, etc., Required, Is in two formats, one key, value pairs or list of objects with (key and label props, required if object)
- Actions: Defines the actions that can be performed on the table rows, like edit, delete, etc. When rendering the row is password so that dynamic actions can be generated for each row. 

Grid, Row, and Column Components:
- Gap: Defines the space between items in the grid/row (small, medium, big, x-small, etc.).
- Cols: Defines the number of columns in the grid (e.g., 2, 4, 6).
- Nodes: Defines child components or nested components within the grid/row/column.
- For each node we can define a span which determines how many cols a node takes for Grid only.

Null and HorizontalRuler (Alias HR)
- Null used as a placeholder to help with grid and The ruler to delimit sections of a page.

Select Component:
- label: A string that specifies the label of the select input. Required
- id: The unique identifier for the select element. Required.
- options: The options to display in the dropdown. Typically, a key-value pair where the key is the internal value and the value is the display text. Required.
- value: The default value to be selected. It should match one of the keys from the options list.
- required: A boolean flag indicating if the selection is mandatory.
- disabled: A boolean flag to disable the select element.


TypeAhead Component:
TypeAhead component (also known as autocomplete or search-as-you-type) 
It is an input with addition attributes. It renders an input field internally.
- id: The unique identifier for the typeahead element. Required
- label: A string that specifies the label for the typeahead input field. Required
- query: A key that specifies which field to search for in the dataset (e.g., search by name or email).
- show: The key in the dataset that should be shown as the suggestion (e.g., show the name of a user).
- Action: Defines the form submission URL. Required
- attach: Specifies the key from the response data that should be used to attach to the input field.
- showLabel: A boolean that indicates whether the label for the typeahead field should be shown.
- placeholder: A text placeholder that appears when no input is given.
- required: A boolean flag indicating whether the field is mandatory.
- disabled: A boolean flag to disable the typeahead input.

Key Takeaways:
- Grid, Column, Row, Heading, HR all have margins, paddings
- Grid, Column and Row have gaps.
- Component Types: You can define any component, like Heading, Button, Input, Form, Table, and more.
- Styling Flexibility: Margin, padding, and size can be adjusted to control the spacing and appearance of each component.
- Dynamic Actions: Components can include actions (e.g., links, form submission) with dynamic URL generation using parameters.
- Nested Components: Components can be nested within containers like Row, Column, or Grid, giving you flexible layouts.
- Component Variability: Each component comes with its own specific properties, giving full control over how it appears and behaves.