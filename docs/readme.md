### Declarative UI Schema for Web Applications

This schema defines a declarative structure for building flexible and responsive user interfaces. By using a collection
of reusable components, it enables rapid development of dynamic web applications. This documentation outlines the
available components, their attributes, and how to use them effectively to construct your ui.

### Introduction
   YAML files are used to describe UI components such as headings, buttons, inputs, forms, tables, grids, rows, columns, and actions. Each component has specific attributes defined in camel case (except "type") that need to be specified within the YAML structure.  
   The key representing the component should be written in CamelCase (e.g., `Grid`, `Input`, `View`), but the value of the `type` field is case-insensitive and can match any casing.

#### Basic Component Structure
   A typical component definition starts with a type key followed by its properties:
```yaml
  - type: Component
    property1: value
    property2: value
```
Example using the Button component:
```yaml
- type: Button
  label: "Click Me"
  class: primary
  role: button
```

# Real-World Examples

These examples demonstrate PixelWrap schemas and integrations from live projects.

## Acura ERP Dashboard

An example dashboard schema from the Acura ERP project (`resources/pixelwrap/dashboard/dashboard.yaml`):

```yaml
- type: Heading
  margin: bottom-smaller top-smallest
  label: "Dashboard"
- type: Listing
  gap: smaller
  dataset: items
  nodes:
    - type: Card
      label: "{label}"
      margin: none
      span: small-full medium-half large-quarter
      nodes:
        - type: Text
          label: "{value}"
```

In the Blade layout (e.g. `resources/views/layouts/app.blade.php`), render the PixelWrap container:

```blade
@section('content')
    {{-- other content --}}
    @yield('pixelwrap-container')
@endsection
```

## Fleetr Dashboard & Onboarding

### Dashboard Card Layout

Example YAML from Fleetr (`resources/pixelwrap/dashboard/dashboard.yaml`):

```yaml
- type: Heading
  margin: bottom-smaller
  label: "Dashboard"
- type: Listing
  gap: smaller
  dataset: items
  nodes:
    - type: Card
      showLabel: false
      padding: smaller
      span: half medium-third large-quarter
      nodes:
        - type: Row
          align: center
          gap: x-small
          nodes:
            - type: Icon
              name: "{icon}"
              size: smaller
            - type: Column
              gap: none
              nodes:
                - type: Heading
                  label: "{value}"
                  size: small
                - type: Heading
                  label: "{label}"
                  size: smaller
```

### Onboarding CTA Example

Render a PixelWrap component inline within a Blade view (`resources/views/vehicle/onboarding.blade.php`):

```blade
{!! pixelwrap()
     ->renderComponent([
         'type'   => 'Button',
         'role'   => 'link',
         'label'  => 'Add Vehicle',
         'icon'   => 'plus',
         'action' => (object)[
             'route'  => 'vehicle.create',
             'params' => ['account' => request()->user()->accounts()->first()->id ?? 1]
         ]
     ])
!!}
```

### Auth Flow Example

Using PixelWrap to render the user registration form in `resources/views/auth/register.blade.php`:

```blade
{!! pixelwrap()->render("user/register") !!}
```

## Laravel Helper Functions

PixelWrap ships global helper functions for easy rendering and integration in Laravel apps.

- **pixelwrap()**: Returns the `PixelWrapRenderer` service instance from the container.
- **renderPixelWrapPage(string $page, array $data = [])**: Alias for `app('pixelwrap')->render($page, $data)` that returns a Blade `View`.
- **pixelwrap()->render(string $page, array $data = []): string**: Render a YAML schema file located at `resources/pixelwrap/{page}.yaml`.
- **pixelwrap()->renderComponent(array $component): string**: Render a single inline component definition without a separate schema file.
- **pixelwrap_resource(string $path = null): string**: Get the base path (or a subpath) to the `resources/pixelwrap` directory.
- **pixel_insert_icon(string $icon): string**: Load raw SVG contents for an icon from the configured icon paths.

## FileController Usage

The `FileController` (in `src/Controllers/FileController.php`) extends `PixelController` to provide a standard CRUD interface for Eloquent models using YAML schemas:

```php
class FileController extends PixelController
{
    protected $class = Model::class;        // Eloquent model class to manage
    protected $authorization = false;       // Toggle auth checks
    protected $perPage = 15;               // Default pagination size

    public function index()   // Renders the 'listing' schema with paginate()
    public function create()  // Renders the 'create' schema
    public function store()   // Persists a new record and redirects
    public function edit()    // Renders the 'edit' schema
    public function show()    // Renders the 'show' schema
    public function update()  // Updates the model and redirects back
    public function destroy() // Deletes the model and redirects
}
```

## Reference: Theme Tokens

Abstract tokens for spacing, sizing, rounding, and typography are defined in the Tailwind theme definitions (e.g., `vendor/pixelwrap/tailwind/src/definitions.php`). To regenerate this list, extract the array keys only:


See [Token Reference](tokens.md) for the current list of theme‑independent tokens.

### Common Attributes Across Components

These attributes define the layout, spacing, and appearance of components in a consistent and theme-aware way. Most components in PixelWrap support these options to ensure a uniform UI experience.

#### Compound Components

Components such as `Card`, `Modal`, `Form`, `Section`, and `Row` are considered *compound components*. They require a `nodes` array that contains one or more child components. These children are rendered recursively, allowing deep nesting and flexible layout structures.

Compound components validate and apply layout utilities like alignment, justification, and growth according to the theme configuration.

### Components

The following components are available in PixelWrap:

  - [View](#view)
  - [Text](#text)
  - [PlaceHolder](#placeholder)
  - [Tabs](#tabs)
  - [Tab](#tab)
  - [Progress](#progress)
  - [PixelIcon](#pixelicon)
  - [Navbar](#navbar)
  - [Sidebar](#sidebar)
  - [Image](#image)
  - [TypeAhead](#typeahead)
  - [Form](#form)
  - [Table](#table)
  - [Field](#field)
  - [Select](#select)
  - [Button](#button)
  - [TextArea](#textarea)
  - [Input](#input)
  - [HorizontalRuler](#horizontal-ruler)
  - [Section](#section)
  - [Column](#column)
  - [Row](#row)
  - [Grid](#grid)
  - [Heading](#heading)
  - [Listing](#listing)
  - [Modal](#modal)
  - [Card](#card)
  - [Breadcrumb](#breadcrumb)
  - [Timeline](#timeline)

---

#### Timeline

The `Timeline` component displays a vertical or horizontal sequence of events based on a dataset. Each event is styled and arranged along a central line using themed icon sizes and direction-aware classes.

**Attributes:**

- **dataset** (required): The name of the dataset to iterate over. Must exist in the current data context.
- **orientation**: Either `vertical` (default) or `horizontal`. Controls the direction of the timeline.
- **iconSize**: The size of the icon displayed for each event. Must match a theme-defined token for the chosen orientation. Examples: `small`, `medium`, `big`, etc.
- **nodes** (required): An array of child components to render for each dataset item. As a compound component, Timeline supports full nested composition using the standard node system.
- **rounded**, **margin**, **padding**: Optional abstract layout and styling controls.

**Behavior:**

- Timeline is a compound component and renders its child nodes recursively for each item in the dataset.
- Timeline items are rendered by iterating over the `dataset`.
- The orientation determines both the layout and the set of valid `iconSize` tokens.
- Errors are thrown if the dataset is missing or if an invalid `iconSize` is provided.
- Each item in the dataset shares the same layout defined in `nodes`.

**Example:**

```yaml
- type: Timeline
  dataset: activity_logs
  orientation: vertical
  iconSize: small
  nodes:
    - type: Field
      label: "Action"
      value: "{action}"
    - type: Field
      label: "Timestamp"
      value: "{timestamp}"
    - type: Text
      label: "{description}"
      size: small
```

> Tip: Define your `iconSize` tokens in the theme under `verticalTimelineIconSizes` or `horizontalTimelineIconSizes` to control their appearance.

---

#### Breadcrumb

The `Breadcrumb` component displays a navigation trail using linked segments. It helps users visualize their current location within a nested page hierarchy and navigate back.

**Attributes:**
- **links** (required): An array of breadcrumb steps. Each step can include a `label`, `route`, `params`, or static `url`. Optionally, an `icon` can be specified.
- **margin**: Optional abstract spacing around the breadcrumb.
- **rounded**: Optional rounding configuration (mostly ignored visually for breadcrumbs but accepted for consistency).

**Behavior:**
- The first breadcrumb item automatically displays a default home icon unless a custom `icon` is provided.
- RTL layout is supported using dynamic spacing utilities.
- Links are constructed using the PixelWrap `Link` utility class, which supports dynamic interpolation of routes and parameters.
- Breadcrumb segments are separated by icons to indicate progression through the hierarchy.
- Rendering uses a responsive layout with accessibility support (`aria-label="Breadcrumb"`).

**Example:**
```yaml
- type: Breadcrumb
  margin: y-smaller
  links:
    - label: "Accounts"
      route:
        name: "account.index"
    - label: "{account.name}"
      route:
        name: "account.show"
        params:
          account: "{account.id}"
    - label: "{identification}"
      route:
        name: "vehicle.show"
        params:
          account: "{account.id}"
          vehicle: "{id}"
    - label: "Edit"
      url: "#"
```

> Tip: You may pass dynamic placeholders like `{account.name}` to interpolate values based on the current data context.

---

#### Card

The `Card` component is a visual container used to group related UI elements with optional framing, label, and spacing. It allows for structured layouts and is ideal for grouping form fields, summaries, or lists inside a themed block.

**Attributes:**

- **label**: Optional title of the card. Will only display if `showLabel` is `true`.
- **showLabel**: Boolean to toggle display of the card's label/title (default is `true`).
- **nodes**: Required. An array of child components to render inside the card.
- **rounded**: Optional. Abstract roundness token (`smallest`, `big`, `full`, etc.). May be capped internally based on theme or layout limits.
- **padding**, **margin**, **span**: Optional abstract layout controls for spacing and sizing.

**Behavior:**

- Card components are compound and recursively render their `nodes`.
- Roundness is applied consistently with global or overridden settings but may be capped (e.g., at `jumbo` or `biggest`).
- Can optionally display a label as a header above content.

**Example:**

```yaml
- type: Card
  label: "Account Details"
  showLabel: true
  rounded: bigger
  nodes:
    - type: Row
      gap: x-small
      nodes:
        - type: Input
          id: first_name
          label: First Name
        - type: Input
          id: last_name
          label: Last Name
```

---

#### Modal

A dialog or overlay used for creating, editing, or confirming actions without navigating away from the current view.

**Attributes:**
- **label**: Title text shown at the top of the modal.
- **id**: Unique modal identifier (used for toggle and hide actions).
- **icon**: Optional icon to display alongside label.
- **nodes**: Main content body.
- **footer**: Optional. Defines a custom footer layout. Accepts a component block, typically a Row or set of Buttons.
- **size**: Optional modal width token (`smaller`, `default`, `bigger`).
- **static**: If true, disables backdrop click and ESC key to close the modal. Useful for critical actions.
- **showClose**: Boolean to toggle the close button (top-right corner). Defaults to true.

> Note: This component supports nested composition through the `nodes` key and renders each child component recursively using the compound node system.

```yaml
- type: Modal
  label: Link Device
  id: linkDevice
  icon: link
  nodes:
    - type: TypeAhead
      label: Enter Device IMEI/SIM Card Number
      id: device_id
      required: true
      attach: id
```

**Behavior:**
- If a `footer` block is provided with nested `nodes`, it is rendered inside a horizontal `Row`. Otherwise, a default dismiss button is shown.

---

#### Listing

The `Listing` component is a wrapper that renders a repeated structure (such as forms, modals, or cards) for each item in a dataset. It supports pagination, dynamic context resolution, and empty state handling.

**Attributes:**
- **dataset** (required): The name of the dataset in the current data context. This can be a static string or an interpolated placeholder.
- **nodes** (required): An array of child components to be rendered for each item in the dataset.
- **showEmptyMessage**: Optional boolean to show or hide a placeholder message when the dataset is empty. Defaults to `true`.

**Behavior:**
- The component resolves the dataset from the current data context using the `dataset` key.
- If the dataset is an instance of `Paginator` or `LengthAwarePaginator`, pagination controls are automatically activated.
- Each item in the dataset is rendered by cloning and processing the `nodes` definition.
- If `showEmptyMessage` is true and the dataset is empty, a fallback empty message block is rendered.

**Example:**
```yaml
- type: Listing
  dataset: vehicles
  showEmptyMessage: true
  nodes:
    - type: Card
      label: "{identification}"
      nodes:
        - type: Field
          label: "Make"
          value: "{make}"
        - type: Field
          label: "Model"
          value: "{model}"
```

#### Typography

Typography sizing is controlled using abstract size tokens. These tokens provide a consistent way to scale text across headings, labels, buttons, and other text-based components. The interpretation of these tokens depends on the active theme.

##### Supported Tokens

- `smallest`, `smaller`, `small`, `big`, `bigger`, `biggest`

These tokens ensure that text is responsive and scalable while remaining theme-agnostic. Their exact rendering (e.g., font size, weight) is defined in the active theme configuration.

##### Usage Example

```yaml
- type: Heading
  label: "Overview"
  size: biggest
```

In this example, `size: biggest` instructs the theme to apply its largest heading style.

#### Sizing Tokens

These are abstract size labels used across multiple layout properties (padding, margin, gap, border, rounding):

- `none`, `smallest`, `smaller`, `small`, `big`, `bigger`, `biggest`

These map internally to CSS utility classes, ensuring consistent spacing across components.

#### Rounded Corners

Roundness in PixelWrap is defined using the same abstract sizing tokens used across layout, ensuring consistent styling. Rounded corners can be applied globally via theme configuration or overridden per component.

##### Available Tokens

- `none`, `smallest`, `small`, `medium`, `big`, `bigger`, `biggest`, `jumbo`, `full`

These tokens represent increasing levels of roundness and are interpreted by the active theme to apply the corresponding visual styles.

##### Example:

```yaml
- type: Button
  label: Save
  variant: primary
  rounded: big
```

In this example, the `Button` component explicitly overrides the global roundness setting to apply a `big` corner radius.

> Note: Some components like `Modal`, `Table`, and `Card` enforce a **maximum allowed roundness**. While you can configure `rounded: full`, these components will cap the visual effect at their predefined maximum (e.g., `biggest`, `jumbo`, or `full` depending on context). This ensures visual consistency across layouts. Smaller values may still be overridden internally unless explicitly allowed. The Modal component also enforces a maximum roundness threshold to maintain visual consistency within overlays.

#### Axis & Directional Prefixes

You can prefix sizing tokens with a direction or axis to target specific areas:

- `x-`, `y-`: Horizontal (`x`) or vertical (`y`) axis — e.g., `margin: x-small`
- `top-`, `bottom-`, `left-`, `right-`: Single-side control — e.g., `padding: top-bigger`

Combined keys like `x-smallest` or `bottom-biggest` are automatically merged.

   
#### Common Props
##### Actions
Used for building links, form submissions.

- link: Optional string containing a hyperlink. Can include dynamic parameters.
- params List of key-value pairs for passing data through the action.

#### Heading

The `Heading` component is used to define section headers or titles in your interface. It supports styled text, embedded actions, and theme-aware typography.

**Attributes**:

- **label**: The visible text displayed in the heading. Can use placeholders (e.g., `{user.name}`).
- **size**: Abstract typography size. Must be one of: `smallest`, `smaller`, `small`, `big`, `bigger`, `biggest`. Mapped to theme heading sizes.
- **actions**: Optional array of inline action buttons rendered alongside the heading. Each action accepts typical `Button` fields like `role`, `label`, `action`, and `variant`.
- **rounded**, **padding**, **margin**: Optional layout and style tokens inherited from the base `Text` class.

**Behavior**:

- The heading text is styled using theme-defined typography for the specified size.
- If `actions` are provided, they're rendered inline or adjacent, depending on layout rules.
- All common `Text` properties are inherited, including filters, interpolation, and spacing controls.

**Examples**:

```yaml
- type: Heading
  label: "Dashboard"
  size: biggest
```

```yaml
- type: Heading
  label: "{user.name}"
  size: big
  actions:
    - type: Button
      role: link
      label: "Edit"
      variant: secondary
      action:
        route: user.edit
        params:
          id: "{user.id}"
```

> Note: Action buttons added to a heading behave like standard `Button` components and support routing, modal toggles, and styling variants.


#### Grid

The `Grid` component is used to define multi-column layouts and is ideal for arranging UI components in a structured, responsive grid system. It allows for flexible column spans, nested compositions, and theme-aware layout behavior.

**Attributes:**

- **cols**: Defines the number of columns in the grid. Accepts abstract column span tokens (e.g., `2`, `3`, `4`, `6`, `12`, or `full`).
- **nodes**: Required. An array of child components to place within the grid.
- **gap**: Optional. Specifies spacing between grid items (abstract size tokens).
- **margin**, **padding**, **rounded**: Optional layout styling properties.
- **alignment**, **justify**: Optional utilities to control item positioning within the grid.

> Each child node in a `Grid` may specify a `span` attribute to determine how many columns it occupies. This is validated against the current theme’s column span options.

**Behavior:**

- Grid validates `cols` and child `span` values against the current theme’s supported tokens.
- Internally, span tokens like `span: 6` must be compatible with the overall `cols` configuration.
- Children are rendered in order and laid out in rows as needed.

**Example:**

```yaml
- type: Grid
  cols: 6
  gap: x-small
  nodes:
    - type: Input
      id: name
      label: Name
      span: 3
    - type: Input
      id: email
      label: Email
      span: 3
    - type: Button
      role: submit
      label: Save
      span: 6
```

> This example places two input fields side by side, each occupying half the grid, and a full-width submit button underneath.

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

> Note: This component supports nested composition through the `nodes` key and renders each child component recursively using the compound node system.

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

#### Column

The `Column` component is a layout container used to organize child components vertically. It is functionally similar to the `Row` component but arranges its children from top to bottom instead of left to right.

Like other compound components, `Column` supports layout options and spacing utilities for refined control over how nested components are displayed.

**Attributes:**

- **gap**: Vertical spacing between child components.
- **nodes**: Array of child components to render in vertical sequence.
- **margin**: Optional spacing around the column.
- **padding**: Optional inner spacing inside the column.
- **rounded**: Optional corner roundness control (theme-respecting, capped based on internal limits).

> Note: This component supports nested composition through the `nodes` key and renders each child component recursively using the compound node system.

**Example:**

```yaml
- type: Column
  gap: y-small
  nodes:
    - type: Input
      id: address
      label: Address
    - type: Input
      id: postal
      label: Postal Code
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

> Note: This component supports nested composition through the `nodes` key and renders each child component recursively using the compound node system.

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

The `HorizontalRuler` component is used to create a visual separator, typically rendered as a horizontal line, that divides content or sections within a UI. This component is intended for simple visual separation only and supports a limited set of props. It uses the `hr` template directly and does not allow for child nodes or advanced configuration.

**Attributes:**

- **class**: Optional. Visual theme/style class (`primary`, `secondary`, `danger`, etc.).
- **width**: Optional. Sets the thickness or visual width of the ruler.
- **margin**, **padding**: Optional spacing utilities.
- **rounded**: Technically supported but visually ignored in most cases.

> Note: The HorizontalRuler component does not support child nodes or advanced configuration. It renders using a theme-defined horizontal line template.

```yaml
- type: HorizontalRuler
  class: primary
```

> The HorizontalRuler component uses the `hr` template internally and is intended for simple visual separation only.
#### Input

The `Input` component is used to collect various types of user input, including text, numbers, passwords, emails, and more. It is highly flexible, supporting a broad set of attributes and behaviors for dynamic and accessible forms.

**Attributes:**

- **id**: A unique identifier for the input field (required).
- **label**: Descriptive label for the input field.
- **fieldType**: Input field type. Supported values: `text`, `email`, `password`, `number`, `hidden`, `date`, `readonly`.
- **placeholder**: Optional placeholder text.
- **default**: Fallback value when no other value is present.
- **value**: Bound value. Can include dynamic placeholders.
- **required**: Boolean to indicate required input (default: true).
- **disabled**: Boolean to disable the field.
- **autocomplete**: Enables browser autocomplete (default: false).
- **icon**: Optional icon (e.g., `mail`, `user`). Adds spacing automatically.
- **showLabel**: Boolean to show or hide label (automatically disabled for `fieldType: hidden`).
- **rounded**: Optional. Uses abstract roundness tokens. Default is `true` but may be capped by theme.
- **padding**, **margin**, **size**, **variant**: Optional style controls using abstract tokens.

**Behavior:**

- If `fieldType` is set to `hidden`, the input is rendered without a label.
- Adding an icon enables left padding automatically for visual spacing.
- Supports full value interpolation and input validation as inherited from `InputContract`.
- Roundness is theme-capped even though `rounded: true` is default.

**Example:**

```yaml
- type: Input
  id: email
  label: "Email Address"
  fieldType: email
  placeholder: "Enter your email"
  icon: mail
  rounded: big
  autocomplete: true
```

---

#### TextArea

The `TextArea` component is used for capturing longer blocks of text input from users. It extends the `InputContract` base behavior but renders a multi-line input field.

**Attributes:**

- **id**: Unique identifier for the text area (required for data binding and form interaction).
- **label**: Text label displayed above or beside the textarea.
- **rows**: Number of visible text lines. Defaults to `3`.
- **value**: The initial content or dynamic expression shown in the text area.
- **default**: Fallback value used when no value is provided or resolved.
- **placeholder**: Text displayed inside the field when empty.
- **disabled**: Boolean toggle to disable the input.
- **rounded**: Optional roundness level (`none`, `small`, `bigger`, `full`, etc.). Subject to theme-imposed maximums.
- **margin**, **padding**, **variant**, **size**: Optional abstract styling tokens.

**Behavior:**

- Inherits input behavior from `InputContract`, including label display, value resolution, disabled logic, and placeholder interpolation.
- Applies primary theme styles for both the textarea and its label.
- Automatically adjusts styling for disabled states using theme configuration.
- Supports abstract spacing and rounding via theme tokens.

> Note: While you can set `rounded: full`, the maximum visible roundness may be capped based on component type. For example, TextArea may limit roundness to `jumbo` or `biggest` as defined by theme constraints.

**Example:**

```yaml
- type: TextArea
  id: description
  label: Description
  rows: 5
  placeholder: "Enter your notes here..."
  rounded: medium
```

> Tip: Use `rounded: full` for pill-shaped fields. Theme constraints may cap maximum roundness depending on context.

#### Button

The Button component is used to trigger actions, submit data, navigate to routes, or control modals. Its behavior and style are controlled by a rich set of attributes, supporting a wide range of use-cases from simple form buttons to complex icon-only navigation triggers.

**Attributes:**

- **label**: Text to display inside the button.
- **role**: Purpose of the button. One of `submit`, `button`, `reset`, `link`.
- **action**: Required if `role` is `link`. Defines route navigation behavior. Accepts a route name and optional parameters.
- **variant**: Style preset. Valid options are defined in the theme (`primary`, `secondary`, `danger`, `icon`, etc.).
- **size**: Controls padding and font size. Available tokens are theme-specific (`smallest`, `small`, `big`, etc.).
- **icon**: Optional icon (required for `variant: icon`).
- **rounded**: Boolean or token. Accepts abstract values (`smallest`, `jumbo`, etc.). Some components may cap the maximum roundness.
- **modalToggle**, **modalShow**, **modalHide**: Trigger modal visibility behaviors by toggling, showing, or hiding a modal with the given identifier.
- **showLabel**: Whether to display button label next to icon (defaults to true).
- **value**: The value submitted if button is used in a form.
- **name**: Name attribute for identifying the button input.

**Examples**

Submit button inside a form:

```yaml
- type: Button
  role: submit
  label: Save
  variant: primary
```

Icon-only button that toggles a modal:

```yaml
- type: Button
  role: button
  icon: plus
  variant: icon
  size: smallest
  modalToggle: addModal
```

Link button with route-based navigation:

```yaml
- type: Button
  role: link
  label: View Details
  size: small
  variant: secondary
  action:
    route: vehicle.show
    params:
      id: "{vehicle_id}"
```

Icon-only link button for dashboard navigation:

```yaml
- type: Button
  role: link
  label: "Dashboard"
  variant: icon
  icon: home
  size: smallest
  action:
    route: dashboard.index
```

> Note: If `variant: icon` is used, an `icon` must be provided. When `role: link` is set, the `action` field becomes mandatory to construct the navigation URL.


#### Badge

The `Badge` component is a compact visual element used to display short text labels, statuses, or numeric indicators. It inherits all behavior and styling from the `Text` component but is intended for inline use with minimal padding and prominent styling to stand out.

**Attributes:**

All attributes supported by the `Text` component are available, including:

- **label**: The content to display inside the badge (can include dynamic placeholders).
- **variant**: Visual style token (e.g., `primary`, `success`, `warning`, `danger`, etc.).
- **size**: Typography size token controlling the font size (`smallest` to `biggest`).
- **rounded**: Optional toggle to apply rounded corners.
- **padding**, **margin**: Optional spacing control for placement.
- **class**: Additional theme-level styling modifiers (if needed).

**Example:**

```yaml
- type: Badge
  label: "Active"
  variant: success
  size: smaller
  rounded: true
```

> Badges are typically used to indicate state, priority, or category — e.g., "New", "Beta", "Failed", "Admin", etc.

#### Field

The `Field` component is a simple key-value display element ideal for rendering read-only properties from an object or data context. It is typically used in read-only forms, summaries, or detail views where editing is not allowed or needed.

**Attributes:**

- **label**: A label or title shown before the value.
- **value**: The dynamic value or static string to be displayed. Can include placeholders like `{user.name}`.
- **default**: A fallback value if `value` is empty or undefined.
- **filters**: Optional formatting filters (e.g., `null:-`, `uppercase`, etc.).
- **size**, **variant**, **rounded**: Optional styling tokens inherited from the `Text` base class.
- **padding**, **margin**: Optional spacing controls.

**Behavior:**

- Values are interpolated using the current data context and can be filtered.
- Useful when rendering multiple fields in summary panels or detail cards.

**Example:**

```yaml
- type: Field
  label: "Name"
  value: "{user.name}"
  default: "N/A"
```

> Tip: Use inside components like `Card`, `Row`, or `Column` to layout multiple fields neatly.

#### Table

The `Table` component displays structured tabular data and supports advanced layout and interaction features, including multi-dataset rendering, column/row spans, highlighting, and aggregation.

**Attributes:**

- **dataset**: A single or array of named datasets. Each dataset renders as a separate table section.
- **fields**: Array of column definitions. Each field can define:
  - **key**: Data key in the dataset
  - **label**: Display name for the column
  - **alignment**: Text alignment (`left`, `center`, `right`)
  - **filters**: Optional list of filters (`null:-`, `currency`, etc.)
  - **aggregated**: One or more of `sum`, `avg`, `min`, `max`
- **actions**: Row-level action definitions (e.g., edit, delete, link).
- **highlight**: A key to visually emphasize (can optionally include an action).
- **indexed**: Shows an auto-incremented index column.
- **aggregated**: Enables footers showing column totals or averages.
- **variant**: Visual style preset (`primary`, `secondary`).
- **showHeader**: Whether to display table headers.
- **emptyMessage**: Message shown when no data is present.
- **headers**: Optional titles for multiple datasets.

**Advanced Layout Support:**
- **colspan**, **rowspan**: Internal logic automatically handles merged cells.
- Complex nested data and dynamic filters are supported via the `TableField` abstraction.

**Example:**

```yaml
- type: Table
  dataset: devices
  highlight:
    field: identification
    action:
      name: view
      role: link
      label: View
      size: smallest
      route: "vehicle.show"
      params:
        vehicle: "{id}"
        account: "{account_id}"
  span: 6
  fields:
    - key: identification
      label: Registration
      aggregated: sum
    - key: vin
      label: VIN/Chassis Number
    - key: make
      label: Make
    - key: model
      label: Model
    - key: model_year
      label: Model Year
  actions:
    - name: show
      role: link
      label: View
      size: smallest
      variant: success
      icon: map-2
      route: "vehicle.show"
      params:
        vehicle: "{id}"
        account: "{account_id}"
```

#### Select
The Select component (commonly referred to as a dropdown or combo box) allows users to choose one or more options from a list of predefined options. It is typically used when there are several choices available, but displaying all of them as buttons or input fields would clutter the interface. The **Select** component helps save space and provides an easy-to-use interface for making selections.

**Attributes:**
- **id**: A unique identifier for the select field. This is useful for form submissions or referencing the select element in scripts or styles.
- **label**: The label displayed next to or above the select dropdown, describing what the user should choose. This label typically appears as text, like "Choose a product" or "Select your country."
- **value**: The value that is pre-selected when the dropdown is rendered. This can be a specific value from the list of options.
- **options**: An array or object of available options that the user can select from. These options can be specified as an array of strings, or an array of objects with label and value properties.
- **optionsMap**: An alternative to `options`. Accepts an object defining:
  - `dataset`: The dataset name in context to pull options from.
  - `key`: The field to use as the option's value.
  - `label`: The field to use as the option's display label.

**Behavior:**
- If `optionsMap` is provided, the component will extract key/value pairs from a dataset. Errors are thrown if required fields are missing or invalid.
- Either `options` or `optionsMap` must be provided; if both are missing, an error is generated.

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

```yaml
- type: Select
  id: vehicle_select
  label: "Choose Vehicle"
  optionsMap:
    dataset: vehicles
    key: id
    label: name
```

> Note: You can dynamically generate options from any dataset by using `optionsMap`. Ensure the dataset is present in the current context and includes the specified `key` and `label` fields.

#### Form

The Form component is the container for gathering and submitting user input. It groups multiple UI elements (like text inputs, buttons, and checkboxes) into a logical unit for easy management and submission. Forms are the backbone of user interaction in applications like registration forms, surveys, or any other scenario where user input is required.

**Attributes:**

- **id**: A unique identifier for the form.
- **nodes**: Required. An array of child components to include in the form.
- **method**: HTTP method to use when submitting the form. Default is `post`.
- **action**: Route definition object used to resolve the form submission endpoint.
- **autocomplete**: Optional boolean toggle for browser autocomplete behavior.
- **padding**, **margin**, **rounded**: Optional layout and style attributes.

> Note: This component supports nested composition through the `nodes` key and renders each child component recursively using the compound node system. The `action` field is resolved dynamically using the provided route parameters. Rounded corners and padding are theme-aware and can be overridden per instance.

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




### TypeAhead

The `TypeAhead` component provides an enhanced text input with live search capabilities. It dynamically fetches and displays suggestions from a remote data source as the user types.

#### Configuration Options

| Field         | Description                                                                 |
|---------------|-----------------------------------------------------------------------------|
| `id`          | Unique identifier for the input element. Required.                         |
| `label`       | Label displayed alongside the input. Required.                             |
| `action`      | Link or route used to query the server for results. Required.              |
| `query`       | Query parameter name sent to the server (default: `q`).                    |
| `attach`      | Field from the server result to attach to the final form value (default: `attach`). |
| `show`        | Field from the server result to display in the input field (default: `name`). |
| `list`        | Field to show in the result list (default: `name`).                        |
| `image`       | Optional image field to display in result list items.                      |
| `placeholder` | Placeholder text inside the input.                                         |
| `required`    | Whether the field is mandatory (default: true).                            |
| `disabled`    | Disables the input field when set to true.                                 |

#### Behavior

- `TypeAhead` internally renders an `Input` component.
- Uses the `action` field to fetch search results as the user types.
- Automatically renders a search result dropdown, with support for selecting values to populate hidden form fields.

#### Example

```yaml
- type: TypeAhead
  id: country_search
  label: Country
  action: /api/search-countries
  query: q
  attach: id
  show: name
  list: name
  placeholder: Start typing a country...
```

#### Image

The `Image` component renders a static or dynamic image inside your layout. It is commonly used for profile pictures, logos, thumbnails, or any visual media. It does not support interactive behavior but respects basic layout props.

**Attributes:**

- **url** (required): The image source URL. Can be a static string or dynamic placeholder (e.g., `{user.avatar}`).
- **alt**: Optional alt text for accessibility and fallback rendering.
- **margin**, **padding**, **rounded**: Optional abstract layout and appearance modifiers.
- **width**, **height**: Optional size controls, defined by theme or layout tokens.

**Behavior:**

- Images are rendered as-is using the provided URL.
- Supports global and local styling such as corner rounding and spacing.
- Can be used inline with other components or standalone within layout containers.

**Example:**

```yaml
- type: Image
  url: "{user.avatar}"
  alt: "User Profile"
  rounded: full
  margin: bottom-small
```

> Tip: Use `rounded: full` to create a circular avatar effect, useful for profile thumbnails.

---

#### Sidebar

The `Sidebar` component renders a vertical navigation menu typically used in layouts with persistent or collapsible side panels. It supports hierarchical menu items via the `items` attribute and can be toggled or expanded dynamically.

**Attributes:**

- **id**: Optional unique identifier for the sidebar instance. Defaults to `"sidebar"`.
- **items** (required): An array of navigation items structured using the `MenuItem` abstraction. Supports nesting and grouping.
- **showDrawer**: Boolean to determine whether the sidebar drawer should be rendered (default: `true`).
- **expanded**: Optional. Boolean that toggles expanded/collapsed view state (default: `true`).
- **label**, **rounded**, **margin**, **padding**: Optional layout and style attributes inherited from the base `Text` class.

**Behavior:**

- The sidebar uses the theme-aware `MenuItem` renderer to construct nested navigation trees.
- If `showDrawer` is `false`, the sidebar is hidden (useful for responsive layouts).
- The sidebar label and text properties are inherited and styled according to the current theme.
- `expanded: false` can be used to collapse sidebar sections or icons-only views in advanced layouts.

**Example:**

```yaml
- type: Sidebar
  id: mainNav
  label: "Main Menu"
  showDrawer: true
  items:
    - label: "Dashboard"
      icon: home
      route:
        name: dashboard.index
    - label: "Settings"
      icon: cog
      children:
        - label: "Profile"
          route:
            name: user.profile
        - label: "Security"
          route:
            name: user.security
```

> Note: Menu items are parsed using the `MenuItem` utility and may support dynamic route interpolation, icons, and multi-level nesting.

---

#### Navbar

The `Navbar` component is a horizontal variant of the `Sidebar`, typically used as a top navigation bar. It inherits the same properties and behavior as the `Sidebar`, including menu structure and dynamic expansion support, but is styled and positioned for horizontal layout.

**Attributes:**

- **id**: Optional unique identifier for the navbar instance. Defaults to `"navbar"`.
- **items** (required): An array of navigation items structured using the `MenuItem` abstraction. Supports nesting and grouping.
- **showDrawer**: Boolean to determine whether the navbar drawer should be rendered (default: `true`).
- **expanded**: Optional. Boolean that toggles expanded/collapsed view state (default: `true`).
- **label**, **rounded**, **margin**, **padding**: Optional layout and style attributes inherited from the base `Text` class.

**Behavior:**

- The navbar uses the theme-aware `MenuItem` renderer just like the sidebar.
- Supports horizontal layout styling and positioning through the theme.
- Ideal for top-level navigation menus in dashboard-style layouts.

**Example:**

```yaml
- type: Navbar
  label: "Main Navigation"
  items:
    - label: "Home"
      icon: home
      route:
        name: dashboard.index
    - label: "Profile"
      icon: user
      route:
        name: user.profile
```

---
#### PixelIcon

The `PixelIcon` component is used to render an inline icon using a theme-defined size. It is ideal for visual indicators, action cues, or inline decorative elements and supports dynamic name resolution through interpolation.

**Attributes:**

- **name** (required): The name of the icon to display. Can be static or a dynamic expression (e.g., `{user.icon}`).
- **size**: Optional abstract icon size. Accepts one of the tokens defined in your theme (e.g., `small`, `big`, `bigger`). Defaults to `small`.
- **rounded**: Boolean or abstract roundness token. Ignored visually for most icon use cases but accepted for consistency.
- **margin**, **padding**, **class**: Optional styling controls.

**Behavior:**

- The icon name is interpolated at runtime using the current data context.
- The icon is styled using a size class from the theme based on the `size` token.
- Theme-defined icon sizes control visual scale and spacing.
- The component is typically used within buttons, lists, or navigation elements.

**Example:**

```yaml
- type: PixelIcon
  name: user
  size: bigger
```

Or with dynamic binding:

```yaml
- type: PixelIcon
  name: "{status_icon}"
  size: small
```

> Tip: Use `PixelIcon` inside buttons or badges to visually enhance navigation and status indicators. Icon rendering relies on theme definitions and may be implemented using font-based icons, SVGs, or custom inline elements depending on your setup.

---


#### Progress

The `Progress` component is used to visually represent the completion status of a task or process. It displays a progress bar along with an optional percentage label.

**Attributes:**

- **value** (required): A number between `0` and `100` representing the progress percentage. Dynamic expressions (e.g., `{completion}`) are supported.
- **showLabel**: Boolean indicating whether to display the percentage label above or inside the progress bar. Defaults to `true`.
- **rounded**, **margin**, **padding**, **class**: Optional layout and style customizations.

**Behavior:**

- The component interpolates and clamps the value between `0` and `100`.
- If `showLabel` is enabled, the value is displayed alongside the visual bar.
- Styling is applied using the current theme and may include size, color, or border options.

**Example:**

```yaml
- type: Progress
  value: "{user.profile_completion}"
  showLabel: true
  rounded: medium
  margin: y-small
```

---

#### Tab

The `Tab` component is used to define individual tab panels in a tabbed interface. Each `Tab` can contain nested components and is typically used alongside a `Tabs` container (not shown here) to organize content in a tab-switching layout.

**Attributes:**

- **label** (required): The visible label of the tab. This is displayed on the tab navigation.
- **icon**: Optional icon name to display alongside the label. Can be static or interpolated.
- **id**: Optional unique identifier for the tab. If omitted, a unique one will be generated automatically.
- **nodes**: Required. An array of child components to render within the tab content.
- **margin**, **padding**, **rounded**: Optional layout styling tokens.

**Behavior:**

- Tabs are compound components that support recursive rendering of `nodes`.
- The `label` is required and determines the text displayed on the tab selector.
- `icon` is optional and may be displayed before the label depending on theme rules.
- A unique `id` is auto-generated if not provided.

**Example:**

```yaml
- type: Tab
  label: "Details"
  icon: info
  nodes:
    - type: Field
      label: "Account ID"
      value: "{account.id}"
    - type: Field
      label: "Status"
      value: "{account.status}"
```

> Tip: Use multiple `Tab` components inside a parent container to create a full tabbed layout experience.

---

#### Tabs

The `Tabs` component is a container that organizes multiple `Tab` components into a tabbed interface. It controls the switching logic and renders the active tab content dynamically based on the configuration.

**Attributes:**

- **tabs** (required): An array of tab definitions. Each tab is internally treated as a `Tab` component and must include at least a `label` and optionally an `icon`, `id`, and `nodes`.
- **id**: Optional unique identifier for the entire tab group. If not provided, a unique ID is auto-generated.
- **rounded**, **margin**, **padding**: Optional layout tokens that control spacing and styling of the tab container or selected tab.

**Behavior:**

- Each item in `tabs` is parsed as a `Tab` and rendered accordingly.
- Tabs are navigable using the visual navigation strip, and clicking on each will render the corresponding `nodes` block.
- Internally uses the `PixelWrapRenderer` to hydrate and render each `Tab` node.

**Example:**

```yaml
- type: Tabs
  id: settingsTabs
  tabs:
    - label: "General"
      icon: cog
      nodes:
        - type: Field
          label: "App Version"
          value: "v1.2.3"
    - label: "Security"
      icon: shield
      nodes:
        - type: Input
          id: password
          label: "Change Password"
```

> Tip: Use `Tabs` when organizing complex forms, grouped settings, or multi-step workflows into distinct tabbed sections.


#### PlaceHolder

The `PlaceHolder` component acts as a layout no-op or dummy block. It renders a template-defined placeholder but does not accept content or configuration beyond basic layout styling. This is useful when conditional rendering is required, or when placeholder space needs to be reserved without showing any UI element.

**Attributes:**

- **padding**, **margin**, **rounded**: Optional abstract spacing or corner styling tokens (useful when reserving space).
- **class**: Optional styling overrides.

**Behavior:**

- Renders a theme-defined `null` template (typically a blank or invisible block).
- Does not support child components (`nodes`) or data-driven rendering.
- Commonly used in conditionally evaluated layouts where actual content is missing.

**Example:**

```yaml
- type: PlaceHolder
  padding: y-small
  margin: bottom-small
```

> Tip: You can use `PlaceHolder` to reserve space during loading, conditional layout switches, or when toggling visibility dynamically.


---

#### Text

The `Text` component is used to display styled, non-editable text content within the interface. It supports themable size, spacing, and appearance properties, and can interpolate values from the data context.

**Attributes:**

- **label** (required): The text content to display. Can include placeholders (e.g., `{user.name}`).
- **size**: Abstract typography size. Accepted tokens: `smallest`, `smaller`, `small`, `big`, `bigger`, `biggest`.
- **variant**: Optional visual style token (`primary`, `secondary`, etc.) depending on the active theme.
- **rounded**, **padding**, **margin**: Optional abstract layout tokens inherited from the `Text` base class.

**Behavior:**

- Text is rendered using theme-aware typography rules and can include interpolation.
- It inherits all layout and spacing behaviors supported by the `Text` base component.
- Can be used inside containers, rows, or columns for inline text presentation or labels.

**Example:**

```yaml
- type: Text
  label: "Welcome, {user.name}!"
  size: big
  variant: primary
  margin: bottom-small
```


---

#### View

The `View` component allows you to embed a named view template within your UI layout. This is useful when you want to insert custom Blade or HTML content that is not constructed via PixelWrap YAML components but still fits inside the rendered UI hierarchy.

**Attributes:**

- **name** (required): The identifier of the view to include. This corresponds to a Laravel view path (e.g., `partials.stats`).
- **label**: Optional. Text label for the view block. Inherited from the `Text` base class and can include dynamic placeholders.
- **margin**, **padding**, **rounded**: Optional layout controls for spacing and appearance.

**Behavior:**

- The view is included using Laravel’s view resolution engine and rendered as-is.
- Useful for injecting hand-coded widgets, analytics blocks, or external template snippets into a YAML-based layout.
- You can pass data into the view via the global data context or embedded logic.

**Example:**

```yaml
- type: View
  name: "partials.stats"
  label: "Statistics Panel"
  padding: y-small
```

> Tip: Use `View` to integrate Blade-based fragments into declarative layouts. The component assumes the view is self-contained and theme-compatible.
