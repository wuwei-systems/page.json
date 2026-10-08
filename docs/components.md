# Components

Each page is a nested tree of components. The document describes their inputs,
content, and behavior. The renderer supplies the runtime on each platform.

## Format

Only `type` is required on every node. Individual components may require props.

| Field        | JSON value     | Meaning                                                                                  |
| ------------ | -------------- | ---------------------------------------------------------------------------------------- |
| `type`       | String         | Component name, in lowercase kebab-case e.g. `frame`, `text`, `tab-panel`                |
| `attributes` | Object         | Component props and declarative behavior, in camelCase: e.g. `maxHeight`, `defaultValue` |
| `children`   | Array of nodes | Nested content in document order.                                                        |
| `text`       | String         | Literal content for text-like nodes.                                                     |
| `key`        | String         | Stable identity among siblings.                                                          |
| `style`      | Object         | Presentation overrides.                                                                  |
| `className`  | String         | Space-separated CSS classes.                                                             |

Supported classes, styles, and theme tokens depend on the renderer implementation.

Whole-value bindings such as `"$form.sources"` preserve value types. Interpolation such as `"Hello, {{ form.name }}"` produces text.

---

# Example components

## `frame`

A rectangular region for layout and surfaces. Vertical and horizontal layouts
work like CSS Flexbox and Figma's Auto Layout. Cards, sections, and panels are
compositions of frames and content.

```json
{
  "type": "frame",
  "attributes": { "direction": "vertical", "gap": 16, "padding": 24 },
  "className": "rounded-xl",
  "children": [
    {
      "type": "text",
      "className": "block text-xl font-semibold",
      "text": "Overview"
    },
    {
      "type": "text",
      "className": "block",
      "text": "A page composed from reusable components."
    }
  ]
}
```

Web dimensions use CSS pixels. Tokens, responsive layouts, and positioning
controls are defined by each renderer.

## `text`

We use `type: "text"` for prose, labels, and headings. You can set heading size and weight
through `className` or `style`.

Supply either literal `text` or inline `children`. Literal text is escaped when
rendered as HTML. Markdown interpretation is a renderer extension.

## `link`

Requires `href` in `attributes`. The inline children provide its label. Web renderers
use an anchor. Links may appear inside `text`, but cannot contain other links or
interactive controls.

```json
{
  "type": "link",
  "attributes": { "href": "https://example.com/guide" },
  "children": [{ "type": "text", "text": "Read the guide" }]
}
```

## `tabs` and `tab-panel`

Tabs accept only `tab-panel` children. Each panel requires a unique string `id`
and a string `label` in `attributes`, and accepts ordinary content children.
The first panel is initially selected; empty tabs render nothing.

Selection is local state. Renderers should support keyboard navigation, expose
the selected tab and its panel relationship.

```json
{
  "type": "tabs",
  "children": [
    {
      "type": "tab-panel",
      "attributes": { "id": "overview", "label": "Overview" },
      "children": [{ "type": "text", "text": "An overview." }]
    },
    {
      "type": "tab-panel",
      "attributes": { "id": "details", "label": "Details" },
      "children": [{ "type": "text", "text": "The details." }]
    }
  ]
}
```

## Dynamic composition

These components require a runtime with bindings and conditions. Neither adds
a visual wrapper.

| Type       | Props / behavior                                                                                           |
| ---------- | ---------------------------------------------------------------------------------------------------------- |
| `for-each` | Required `items` array; optional `as` (default `item`) and `key`. Render children once per item, in order. |
| `if`       | Required `when` condition. Render children only when true; a missing condition renders nothing.            |

An empty array renders nothing.
Each iteration binds `item` and the supplied `as` alias within its descendants.
`attributes.key` is evaluated per item; use stable, unique values when items can move.

```json
{
  "type": "for-each",
  "attributes": {
    "items": "$state.entries",
    "as": "entry",
    "key": "{{ entry.id }}"
  },
  "children": [
    {
      "type": "if",
      "attributes": { "when": { "TRUTHY": "$entry.visible" } },
      "children": [{ "type": "text", "text": "{{ entry.title }}" }]
    }
  ]
}
```
