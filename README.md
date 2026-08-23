# My Odoo developmeent environmenet from dev to production


* About Me
* Demo: Setup an Odoo project using uv
    * setup project with uv
    * create odoo/addons/ocadays_2026 module
    * add hatch-odoo backend
    * add new dependency
* How all of this was possible an Odoo project like python project?
    * What's uv
    * new paradigm with **uv run** to keep your **.venv** synced
    * What's a python package ?
    * Did you know OCA release each module in **pypi.org** thanks to **whool** build backend by Stéphane Bidoul ?
    * how package are installed in a python project ?
    * hatch backend and hatch-odoo with super power
        * auto detect package name from project module dependencies
        * stop worring about addon path 
* Reproductibe environement with **uv.lock**
    * benefit of freezing dependencies
    * unfreeeze dependencies
* How to manage un realeased dependencies and easly switch to an OCA conrtibutor
    * python develop
* uvault to go further
    * problem: (reproductible environement using OCA PR's)
    * solution: pushing commit to a fork you own
    => uvault automatically vaults transient VCS references like Open PR's into your organization's vault repository
       - and do more (cf ./uvault)

---

## 🛠️ Presentation & Diagrams Compilation

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
# Generate all SVG diagrams from .mmd source files
npx -y @mermaid-js/mermaid-cli -i images/diagram_pr_dependency.mmd -o images/diagram_pr_dependency.svg -b transparent
npx -y @mermaid-js/mermaid-cli -i images/diagram_gc_hazard.mmd -o images/diagram_gc_hazard.svg -b transparent
npx -y @mermaid-js/mermaid-cli -i images/diagram_vaulting.mmd -o images/diagram_vaulting.svg -b transparent
npx -y @mermaid-js/mermaid-cli -i images/diagram_uvault_workflow.mmd -o images/diagram_uvault_workflow.svg -b transparent
npx -y @mermaid-js/mermaid-cli -i images/diagram_release_lifecycle.mmd -o images/diagram_release_lifecycle.svg -b transparent
```