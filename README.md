# My Odoo developmeent environmenet from dev to production


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
