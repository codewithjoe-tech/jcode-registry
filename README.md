# jcode-registry

The official plugin registry for [jcode](https://github.com/codewithjoe-tech/jcode) — feature-graph code intelligence for AI agents.

## What is this?

`registry.json` is the single source of truth for installable jcode plugins. When you run `jcode add <name>`, jcode fetches this file, looks up the plugin, and installs it via pip.

## Official plugins

| Plugin | pip package | What it does |
|--------|-------------|--------------|
| `fastapi` | `jcode-fastapi` | FastAPI `Depends`/`Security` dependency edges |
| `django` | `jcode-django` | Django URL routing, ORM relations, signal edges |
| `sqlalchemy` | `jcode-sqlalchemy` | SQLAlchemy `relationship` and `ForeignKey` edges |
| `express` | `jcode-express` | Express.js route and middleware edges |
| `mongoose` | `jcode-mongoose` | Mongoose model definition and populate edges |

## Installing a plugin

```bash
jcode add django
jcode add fastapi
jcode add sqlalchemy
```

Any package that declares a `jcode.plugins` entry point is auto-detected — no config needed.

## Submitting a plugin

Want to add your plugin to the registry? Open a pull request that adds an entry to `registry.json`:

```json
{
  "version": 1,
  "plugins": {
    "your-framework": {
      "pip": "jcode-your-framework",
      "description": "One line description of what edges it emits",
      "repo": "https://github.com/your-username/jcode-your-framework"
    }
  }
}
```

**Requirements for acceptance:**
- Package must be published on PyPI
- Package must declare a `jcode.plugins` entry point
- The entry point name must match the pip package name of the framework it targets (e.g. `django`, `flask`, `rails`)
- Must include a README and tests

## Building a plugin

A jcode plugin is a Python package that:

1. Declares a `jcode.plugins` entry point in `pyproject.toml`:

```toml
[project.entry-points."jcode.plugins"]
your-framework = "jcode_your_framework.plugin:create"
```

2. Implements the `EdgePlugin` protocol — a class with `handled_names` and `handle_call`:

```python
from jcode.domain.models import Edge, Node, NodeType

class MyPlugin:
    @property
    def handled_names(self) -> frozenset:
        return frozenset({"some_function"})

    def handle_call(self, call_node, source: bytes, caller: Node):
        # inspect the tree-sitter call_node, return (nodes, edges)
        return [], []

def create() -> MyPlugin:
    return MyPlugin()
```

See any official plugin for a full working example.

---

Made by [Joel Thomas](https://codewithjoe.in) · [jcode on GitHub](https://github.com/codewithjoe-tech/jcode)
