# Page.json

A page as data, not code.

Page.json is an open standard for describing any UI page in a JSON tree format.

Just as MCP standardizes how agents call tools, Page.json standardizes how pages are described as data across applications.

It is:

- Renderable as a web page, mobile view, rich text article, and more.

- Producible by humans, agents, using any editor tool.

- Declarative: Reuses your components. Safe to store and send. No embedded code or dangerous `eval()`.

## Format

A Page.json document is a recursive JSON tree.
Each node has a `type`, and may also include:

- `children`: nested nodes
- `attributes`: component props and declarative behavior
- `style`: optional presentation overrides
- `text`: raw text content for text-like nodes

Example:

```json
{
  "type": "Frame",
  "attributes": {
    "padding": "lg",
    "gap": "md"
  },
  "children": [
    {
      "type": "text",
      "text": "Hello, world"
    },
    {
      "type": "Button",
      "attributes": {
        "action": {
          "type": "navigate",
          "href": "/about"
        }
      },
      "children": [
        {
          "type": "text",
          "text": "Learn more"
        }
      ]
    }
  ]
}
```

## Live examples

- [Command template](https://wuwei.us/pages/3c137458-2cdc-4163-8c2c-745dd16ef9ba): The archetypal interface for sending commands and messages. It covers agentic chat, CLI (bash/zsh), REPLs (Jupyter, iPython), search bars, and log viewers.

- [Workspace template](https://wuwei.us/pages/5341415b-a99a-409d-bcc6-ffaefb3959b2): Composable with custom grid layout where each pane is resizable
