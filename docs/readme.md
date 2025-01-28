### Declarative UI Schema for Web Applications

This schema defines a declarative structure for building flexible and responsive user interfaces. By using a collection
of reusable components, it enables rapid development of dynamic web applications. This documentation outlines the
available components, their attributes, and how to use them effectively to construct your interfaces.

#### Schema Structure

The schema is structured as a tree of components. Each component is described using attributes that define its behavior,
layout, and data interaction. Components are nested hierarchically within each other using the nodes property, allowing
complex UIs to be built from smaller, manageable pieces.

#### Components

#### Heading

The component is used to define titles or section headers within the UI.
**Attributes**:

- **label** : Text content displayed, which can include dynamic placeholders.
- **size**  : Size of the label. One of `smallest`, `smaller`, `small`, `big`, `bigger`, `biggest`.

**Examples**:

```yaml
-   type: Heading
    label: Social Media Influencers
    size: biggest
```

```yaml
-   type: SubHeading
    label: "Top 100"
```

```yaml
-   type: Heading
    label: "{network.name} Influencers"
```

In the last example given an object below in the current scope, the label will interpolated to  **Facebook Influencers**

```json
{
    "name": "Facebook"
}
```

#### Row

The Row component is a layout container used to organize child components horizontally. It allows for a flexible,
responsive arrangement of UI elements within a defined space. The Row component is commonly used in forms and dashboards
to group related elements together, such as input fields, buttons, or other components, while maintaining an orderly and
consistent structure.

Attributes:

- **gap**: Defines the horizontal or vertical space between child components. For example, gap-x-4 defines horizontal
  spacing, while gap-y-4 defines vertical spacing.
- **nodes**: Contains the child components to be arranged within the row.
- **margin**: Specifies spacing around the row, such as mt-4 for margin-top or mb-4 for margin-bottom.

```yaml
-   type: Row
    gap: gap-small
    margin: top-small
    nodes:
        -   type: Input
            id: product_name
            label: Product Name
        -   type: Input
            id: product_price
            label: Price

```

#### Section

Section component is a composite component that combines a Heading and Subheading along with a Row for organizing child
components. This is useful for creating distinct sections within a page that require a title, a description, and an
organized layout for form fields or other UI elements.

Attributes:

- **title**: The main title of the section, displayed as a heading.
- **subtitle**: A descriptive text displayed below the title, providing additional context.
- **margin**: Optional spacing around the section (e.g., mt-4 for margin-top).
- **nodes**: Child components that are placed inside the section, often structured in rows or columns.
- **gap**: Defines the spacing between child components within the row.

**Example Usage**

```yaml
-   type: Section
    title: "Product Information"
    subtitle: "Please enter the details of the product."
    margin: mt-6
    nodes:
        -   type: Row
            gap: x-small
            nodes:
                -   type: Input
                    id: name
                    label: Name
                -   type: Input
                    id: price
                    label: Price
```

#### Horizontal Ruler

The HorizontalRuler component is used to create a visual separator, typically rendered as a line, that divides content
or sections within a UI. It helps to visually organize the interface by adding a clear boundary or separation between
distinct areas, making the content more readable and structured. Horizontal rulers are often used in forms, settings
pages, or any context where you want to differentiate between groups of related items.

**Attributes:**

- **class** Defines the style of the horizontal ruler, allowing you to customize its appearance. Common styles include
  primary, secondary and danger.
- **width**: Defines the width of the ruler. This can be customized based on the layout needs (e.g., width: 1,2,4,8).

```yaml
- type: HorizontalRuler
  class: primary
```
#### Input

The Input component is used to collect various types of user input, such as text, numbers, or passwords. It is one of
the most commonly used UI elements, allowing users to interact with your application by entering data. The Input
component provides the flexibility to handle different data types and validation rules based on the context in which it
is used.

Attributes:

- **id**: A unique identifier for the input field. This is used to reference the input field in forms or other logic.
- **label**: The label displayed next to or above the input field, describing what kind of data should be entered.
- **fieldType**: Specifies the type of the input field (text, number, password, email).

Example with Password Input:

```yaml
-   type: Input
    id: password
    label: "Password"
    fieldType: password
    required: true
    placeholder: "Enter your password"
```

#### Button

The Button component is used to trigger actions or submit data in a user interface. It is a fundamental component for
any interactive web application, enabling users to submit forms, initiate events, navigate to different routes, or
trigger other behaviors such as opening modals or executing scripts. The **Button** component is highly customizable in
terms of its appearance, behavior, and associated actions.

**Attributes:**

- **label** The text or content displayed inside the button. This could be a string like "Submit", "Save", or "Cancel".
- **class** Style of the button (e.g., primary, secondary, danger, outlined). This control the button’s styling.
- **action** Defines an action (like a route or event) that the button will trigger when clicked.
- **role** Defines the purpose of the button. Common values include:
  - **submit** Triggers a form submission (typically used in forms).
  - **button** A generic button used for triggering any other action.
  - **reset** Resets the form to its default values.
  - **link** A button that behaves like a hyperlink, used to navigate to a route or page.

**Examples**

```yaml
-   type: Button
    role: submit
    label: Submit
    class: primary
```

```yaml
-   type: Button
    role: button
    label: Next
    class: secondary
    action:
        route:
            name: products
            params:
                id: "{productId}"
                action: read
```

#### Table

The Table component is used to display tabular data in a structured format. It is ideal for organizing and presenting
large amounts of data, such as lists of records, items, or transaction details. The Table component allows for flexible
configuration, including defining columns, actions (like editing or deleting), and handling dynamic data through
datasets.

**Attributes**

- **dataset** The data source for the table, typically an array of objects. Each object represents a row, and each key
  in the object corresponds to a column.
- **fields** Defines the columns to be displayed in the table. Each field is a key in the dataset objects and can be
  customized with headers and data.
- **key** What to call the unique identifier of each row in the dataset. This is used for row tracking and interactions.
  Example mapping **id** to **user_id**.
- **keyValue** This is the value field in the dataset that corresponds to the unique identifier (key).
- **actions** A list of actions that can be performed on the rows (e.g., edit, delete). Each action has a label and a
  route to define the behavior.
- **highlight** Specifies which column in the table will be highlighted. The column value is typically used for
  emphasis.
- **emptyMessage** A message displayed when the table is empty (i.e., no data is available to show).

**Example**

```yaml
    - type: Table
      dataset: stores
      key: store_id
      keyValue: id
      indexed: true
      fields:
          name: Name
          location: Location
      highlight: name
      actions:
          - stores:
            label: Sales
            needsKey: true
            key: store
            route:
                name: sales
                params:
```

#### Select
The Select component (commonly referred to as a dropdown or combo box) allows users to choose one or more options from a list of predefined options. It is typically used when there are several choices available, but displaying all of them as buttons or input fields would clutter the interface. The **Select** component helps save space and provides an easy-to-use interface for making selections.

**Attributes:**
- **id**: A unique identifier for the select field. This is useful for form submissions or referencing the select element in scripts or styles.
- **label**: The label displayed next to or above the select dropdown, describing what the user should choose. This label typically appears as text, like "Choose a product" or "Select your country."
- **value**: The value that is pre-selected when the dropdown is rendered. This can be a specific value from the list of options.
- **options**: An array or object of available options that the user can select from. These options can be specified as an array of strings, or an array of objects with label and value properties.

```yaml
- type: Select
  id: skills
  label: "Select Skills"
  multiple: true
  options:
    - js: "JavaScript"
    - py: "Python"
    - rb: "Ruby"
    - java: "Java"
```

#### Form

The Form component is the container for gathering and submitting user input. It groups multiple UI elements (like text inputs, buttons, and checkboxes) into a logical unit for easy management and submission. Forms are the backbone of user interaction in applications like registration forms, surveys, or any other scenario where user input is required.
Attributes:

- **id**: A unique identifier for the form, useful for references in scripts, validation logic, or for targeting the form in CSS styles.
- **nodes**: An array of components that are part of the form. These can include sections, rows, inputs, buttons, etc.


```yaml
- type: Form
  id: registration_form
  nodes:
    - type: Section
      title: "Personal Information"
      subtitle: "Provide your basic details."
      margin: mt-6
      nodes:
          - # Form components here
    - type: Row
      gap: x-small
      margin: top-big
      nodes:
        - type: Button
          role: submit
          label: "Register"
          class: primary
```


#### TypeAhead

The TypeAhead component, also known as an autocomplete or suggestion input, is used to provide users with suggestions as they type. This component helps improve user experience by narrowing down options based on user input. It’s ideal for searching large datasets (like cities, products, or locations) where typing the full name would be inefficient or prone to error.

**Attributes:**
- **id**: A unique identifier for the TypeAhead input. It is useful for form handling, referencing, and CSS styling.
- **label**: The label displayed next to or above the TypeAhead input. This provides context for what the user should be typing (e.g., "Search for a destination").
- **route**: Defines the route or endpoint to fetch the suggestions dynamically based on user input. This could be an API endpoint or a route that returns matching suggestions (e.g., search.center).
- **attach**: Specifies which property of the suggestion object to use for binding or attaching the selected value to the input field. This is useful if you want to store a specific property from the suggestion (e.g., urn to represent the unique ID or location code).

```yaml
  - type: TypeAhead
    id: destination
    route: search.center
    attach: urn
    span: 4
    label: "Destination Center"
    debounce: 300
```
