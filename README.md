# Pythonic Odoo local environment

Reproductible instance from local to production server.

You can checkout the [example branch](https://github.com/petrus-v/odoo-dev-presentation/tree/example)
that illustrate all step of this presentation.


## Presentation & Diagrams Compilation

### Compile Marp Slides to HTML

```bash
npx -y @marp-team/marp-cli slides.md -o slides.html
```
With whatcher mode

```bash
npx @marp-team/marp-cli@latest -w slides.md 
```

### Rebuild Mermaid SVG Diagrams (`images/*.mmd` -> `images/*.svg`)

```bash
# Generate SVG diagrams from .mmd source files
npx -y @mermaid-js/mermaid-cli -i images/diagram_pr_dependency.mmd -o images/diagram_pr_dependency.svg -b transparent
```
