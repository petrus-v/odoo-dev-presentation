---
marp: true
theme: gaia
_class: lead
paginate: true
backgroundColor: #FFFDF0
# backgroundImage: url('https://marp.app/assets/hero-background.svg')
style: |
  :root {
    --color-background: #FFFDF0;
    --color-foreground: #5B0B19;
    --color-highlight: #D51E36;
    --color-dimmed: #E05766;
  }
  section[data-marpit-advanced-background="background"] > div[data-marpit-advanced-background-container]:has(figure[style*="oca-red"]),
  figure[style*="oca-red"] {
    background-color: var(--color-highlight) !important;
  }
  section {
    background-color: var(--color-background);
    color: var(--color-foreground);
  }
  section:not(.lead) {
    font-size: 100%;
    padding: 25px 150px 75px 200px;
    color: var(--color-foreground);
    display: flex;
    flex-direction: column;
    justify-content: left;
    align-items: left;
  }
  section:not(.lead) > h1,
  section:not(.lead) > h2 {
    align-self: center;
    width: 100%;
    margin-top: 0;
    margin-bottom: 0.6em;
    text-align: center;
  }
  section:not(.lead) > h3 {
    align-self: flex-start;
    width: 100%;
    margin-top: 0.8em;
    margin-bottom: 0.3em;
    text-align: left;
  }
  section:not(.lead) > *:not(h1):not(h2):not(h3):not(style) {
    max-width: 100%;
  }
  ul, ol {
    width: fit-content;
    text-align: left;
  }
  blockquote {
    width: 100%;
    box-sizing: border-box;
    margin-top: 0.8em;
  }
  h1 {
    font-size: 1.6em;
    color: var(--color-highlight);
  }
  h2 {
    font-size: 1.25em;
    color: var(--color-foreground);
  }
  h3 {
    font-size: 1.05em;
    color: var(--color-highlight);
  }
  p, ul, ol {
    line-height: 1.35;
    color: var(--color-foreground);
  }
  strong {
    color: var(--color-highlight);
  }
  pre {
    font-size: 85%;
    background-color: #282c34;
    color: #abb2bf;
    border-radius: 6px;
    padding: 12px 16px;
    text-align: left;
    width: 100%;
    box-sizing: border-box;
  }
  pre code {
    color: #abb2bf;
    background-color: transparent;
  }
  code {
    background-color: rgba(224, 87, 102, 0.12);
    color: var(--color-highlight);
    padding: 0.1em 0.3em;
    border-radius: 4px;
    font-weight: bold;
  }
  blockquote {
    background: rgba(224, 87, 102, 0.08);
    border-left: 6px solid var(--color-dimmed);
    padding: 8px 14px;
    font-size: 95%;
    color: var(--color-foreground);
    text-align: left;
  }
  table {
    font-size: 85%;
    width: 100%;
    color: var(--color-foreground);
  }
  th {
    background-color: rgba(224, 87, 102, 0.15);
    color: var(--color-highlight);
  }
---

<style scoped>
h1, h1 strong {
  color: var(--color-foreground) !important;
}
p, strong {
  color: var(--color-dimmed) !important;
}
</style>

![bg fit right](./images/oca-red.png)

# **My Odoo development Environment**

### With `uv`, `hatch-odoo` and **`uvault`**

**Pierre Verkest (APYCOD)**
GitHub: @petrus-v

<!--
I had a dream ! Managed my Odoo project like any other python project !

Let's see the current state !
-->

---

# **About**

**Pierre Verkest** — An enthusiastic independant python developer @ APYCOD

![bg 25%](./images/petrus-v.png)
![bg 50%](./images/apycod.png)

- gh: @petrus-v
- ln: @pierre-verkest
- OCA Contributor
- pytest-odoo Contributor
- Author of `uvault`


<!--
A quick word about me: I'm Pierre Verkest, an independent Python & Odoo developer at APYCOD, an OCA contributor, and creator of uvault.
[joke] Let's be honest: for years, setting up Odoo felt like wrestling with custom scripts and long --addons-path configurations.
But today, thanks to tools like uv, pyproject.toml, whool, and hatch-odoo, Odoo fits naturally into standard Python workflows!
-->

---

# **Agenda**

- **Context**: Historically, Odoo struggled with standard Python tooling (`addons_path`). Today, it integrates seamlessly (`uv`, `pyproject.toml`)!

1. **Odoo as a Standard Python Project**: Modern tooling (`uv`, PyPI, `whool`, Python Namespace Packages)
2. **Dedicated Odoo Build Backend**: Streamlining Odoo packaging with `hatch-odoo`
3. **Unreleased PR Dependencies**: Working with unmerged OCA Pull Requests
4. **DEEP DIVE `uvault`**: Vaulting, local editable dev & release lifecycle

<!--
Here is what we will cover today:
1. Running Odoo as a standard Python project using uv, PyPI, and namespace packages.
2. Using hatch-odoo to handle flexible addon directories and dynamic dependencies.
3. Managing the real-world pain of unreleased OCA Pull Requests.
4. Deep dive into uvault to safely vault PR dependencies, work locally, and handle release lifecycles.
-->

---

<!-- _class: lead -->
# **Odoo as a Standard Python Project**

<!--
Let's dive into part one: initializing and managing an Odoo project like any standard Python package.
Before we start, what is uv and why did we choose it?
uv is Astral's ultra-fast Python package and project manager written in Rust. It replaces pip, virtualenv, pyenv, and pip-tools with a single binary.
We choose uv because it's insanely fast, manages Python versions seamlessly, uses global caching with hardlinks so environment syncing takes milliseconds, and locks dependencies deterministically with uv.lock.
-->

---

# **Project Initialization (`uv init`)**

### Repository setup

```bash
uv init \
  --vcs git \
  --build-backend hatch \
  ocadays-2026-odoo-dev
cd ocadays-2026-odoo-dev
```

### Repository layout

```text
ocadays-2026-odoo-dev/
├── .python-version      # Target Python version (e.g. 3.14)
├── pyproject.toml       # Project metadata & dependencies
└── README.md
```

> ⚡ **Result**: Your project root is initialized with standard Python configuration files!

<!--
Presenter Note: Step 0 (git checkout step-0)
We start with uv init. In just a few milliseconds, uv creates a clean Python repository with pyproject.toml and .python-version.
Notice how clean this structure is—no custom Odoo wrappers, just standard Python config files.
-->

---

# **Installing Odoo Core & Custom Addon**

### Custom addon: `src/odoo/addons/ocadays_2026/__manifest__.py`

```python
{
    "name": "OCA Days 2026 - odoo dev module",
    "version": "19.0.1.0.0",
    "depends": ["web"],
}
```

### Declare Odoo dependency in `pyproject.toml`

```toml
[project]
name = "ocadays-2026-odoo-dev"
version = "0.1.0"
dependencies = [
    "odoo", "lxml>=5.2.1", "lxml-html-clean", "Werkzeug==3.0.1", "PyPDF==5.4.0", "freezegun",
]

[tool.uv.sources]
odoo = { git = "https://github.com/OCA/OCB.git", branch = "19.0" }
```

<!--
Presenter Note: Step 1 (git checkout step-1)
Next, we add our custom module under src/odoo/addons/ocadays_2026.
In pyproject.toml, we declare our project dependencies and point odoo directly to the OCB 19.0 Git repository.
-->

---

# **Running Odoo with `uv run`**

### Paradigm shift: Auto-synced runtime

```bash
# Run Odoo CLI directly (auto-syncs .venv on the fly):
uv run odoo \
  -d ocadays2026 \
  -i ocadays_2026 \
  --stop-after-init
```

- **No more `source .venv/bin/activate`**: `uv run` manages and encapsulates the environment transparently.
- **Effortless Branch Switching**: Switch git branches -> `uv run odoo` re-syncs `.venv` in **milliseconds** from `~/.cache/uv` via hardlinks without even thinking about it!

<!--
Presenter Note: Step 1 (continued)
Now comes the first major game changer: [joke] say goodbye to source .venv/bin/activate!
With uv run odoo, uv automatically manages the virtual environment for you.
If a teammate adds a dependency, or if you switch git branches, uv resyncs your .venv in milliseconds before running Odoo. You don't even have to think about it!
-->

---

# **Python Namespace Packages (`odoo.addons`)**

### PEP 420: Sharing single namespace across packages

- **Concept**: Allows multiple independent distributions (core Odoo, custom module)
  to contribute modules to the same top-level Python package: `odoo.addons`.
- **Configure with `hatchling` build backend** in `pyproject.toml`:

```toml
[tool.hatch.build.targets.wheel]
packages = ["src/odoo"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

> 💡 *Note: We use `hatchling` backend here. Standard backends like `flit` or `uv` also support PEP 420 namespace packages, though configuration syntax varies.*

- **Configure with `uv_build` backend** in `pyproject.toml`:

```toml
[tool.uv.build-backend]
module-name = "odoo.addons"
namespace = true

[build-system]
requires = ["uv_build>=0.12.4,<0.13.0"]
build-backend = "uv_build"
```

---

# **Python Namespace Packages (`odoo.addons`)**

### `--addons-path` is NOW OBSOLETE!

`uv run` links `src/odoo/addons/ocadays_2026` into `.venv` under `odoo.addons` (in editable mode):

```bash
# No --addons-path needed anymore!
uv run odoo -d ocadays2026 -i ocadays_2026 --stop-after-init
```

<!--
Presenter Note: Step 2 (git checkout step-2)
How does Odoo find our custom module? Through PEP 420 namespace packages!
By configuring module-name = "odoo.addons" in pyproject.toml, uv installs our module in editable mode directly into odoo.addons inside .venv.
[joke] That means --addons-path is officially obsolete! Odoo imports our module natively.
-->

---

# **Adding OCA Dependencies (PyPI & `whool`)**

### Update manifest `src/odoo/addons/ocadays_2026/__manifest__.py`

```python
    "depends": ["web", "mis_builder"],
```

### Add PyPI package to `pyproject.toml`

```toml
[project]
...
dependencies = [
    ...
    "odoo-addon-mis-builder>=19.0",
]
```

### Run `uv sync`

```bash
uv sync
# -> Downloads mis_builder, date_range & report_xlsx from PyPI!
```


> 💡 *Note*: you can use `uv add odoo-addon-mis-builder` to do both:
>   - add the package dependency to `pyproject.toml`
>   - sync the virtualenv

<!--
Presenter Note: Step 3 (git checkout step-3)
Now, what if we need an OCA addon like mis_builder?
We simply add mis_builder to our module's manifest, and add odoo-addon-mis-builder to pyproject.toml.
When we run uv sync, it automatically fetches the wheel from PyPI along with all its required dependencies.
-->

---

# **Under the Hood: PyPI & `whool`**

### Did you know EVERY OCA module is published on PyPI?

- OCA module repositories automatically build and publish standard 
  Python wheels on **pypi.org**.
- You probably met the `pyproject.toml` in module directory (such  in `mis_builder`):

  ```toml
  [build-system]
  requires = ["whool"]
  build-backend = "whool.buildapi"
  ```

- Standard PyPI naming convention: `odoo-addon-<module_name>`
  - `mis_builder` ➡️ `odoo-addon-mis-builder`
  - `partner_firstname` ➡️ `odoo-addon-partner-firstname`
- Huge thanks to **Stéphane Bidoul** for the **`whool`** build backend!


### Adding any OCA module is standard Python package management!

```bash
uv add odoo-addon-account-financial-report
```

<!--
You might ask: how is this possible?
Huge shoutout to Stéphane Bidoul for creating whool!
Thanks to whool, every single OCA module is automatically built and published on PyPI as a standard wheel under the odoo-addon- prefix.
[joke] Yes, installing an OCA addon is now literally as simple as uv add odoo-addon-account-financial-report.
-->

---

# **What About Odoo Core Itself?**

### Why does `odoo = {git = "https://github.com/OCA/OCB.git", branch = "19.0"}"` work with `uv`?

- In standard **`odoo/odoo`**, core addons (`web`, `mail`, ...) reside in the root `/addons` folder.
- A standard Python build (`pip wheel .` / `uv`) ignores them ➡️ resulting in `No module named web`!
- **OCA/OCB** natively includes a PEP 517 build backend (by Stéphane Bidoul) that symlinks addons into `odoo/addons` during build.
- Ensures **all package data** (`.xml`, `.csv`, and `static/` directories) are properly bundled in the wheel via `MANIFEST.in` (`graft odoo`).

### Help make upstream Odoo installable!
Upstream Odoo still lacks native PEP 517 wheel packaging:
- Original PR: [#44001](https://github.com/odoo/odoo/pull/44001)
- Forward-port for 19.0: [#232933](https://github.com/odoo/odoo/pull/232933)

👉 **Please upvote 👍 and make noise on PR #232933** to get upstream Odoo to merge it!

<!--
Presenter Note:
Remember in step 1 how we pointed odoo to OCA/OCB in pyproject.toml?
Why OCB and not odoo/odoo?
Because upstream Odoo cannot be built into a proper wheel with standard pip/uv tools!
The core addons like `web` reside in /addons and aren't packaged by default.
Historically, people trying ad-hoc symlinks often ended up with broken installs missing XML views, CSV security rules, or static JS/CSS assets.
OCB solves this completely: Stéphane Bidoul's PEP 517 build backend symlinks addons before wheel creation, and MANIFEST.in's `graft odoo` ensures every single .xml, .csv, and static file is bundled.
There is an open PR on odoo/odoo for 19.0 (PR #232933, forward-ported from #44001).
Go upvote it and make noise so that upstream Odoo finally adopts standard packaging!
-->

---

# **IDE Pro-Tip: Navigating `site-packages` 💡**

### Everything is installed in `.venv/lib/python3.x/site-packages/`

- All OCA modules and Odoo core reside inside your `.venv`.
- **Recommended practice**: Add `.venv/.../site-packages/odoo/addons` to your IDE workspace (VSCodium / VS Code / PyCharm).

### Key developer benefits:
- **Global Code Search**: Search classes, views, and methods across all OCA addons.
- **Seamless Debugging**: Set breakpoints directly inside any third-party OCA module.
- **Go-to-Definition**: Fast navigation to inherited models and methods.

<!--
Presenter Note: (Live Demo hint: show VSCodium sidebar)
Here is a quick pro-tip for your IDE: since all addons live inside .venv/.../site-packages/odoo/addons, add that folder to your IDE workspace!
This unlocks instant global search across all installed OCA addons, jump-to-definition, and allows you to place debug breakpoints anywhere in third-party code.
-->

---

# **Reproducibility & Security: `uv.lock`**

### Deterministic & Tamper-proof Lockfile

- **Cross-platform lockfile**: Multi-OS & multi-Python version locking.
- **Supply Chain Protection**: Verifies SHA256 hashes & Git commits to prevent package tampering.
- **Strict CI & Production Deployment**:
  - `uv sync --locked`: Fails if `uv.lock` is out-of-sync with `pyproject.toml` (CI assertion).
  - `uv sync --frozen`: Installs directly from `uv.lock` without modifying it (Fast Docker/Prod builds).
- **Legacy Export**: `uv export` to `requirements.txt` or standard `pylock.toml` (PEP 751).

> 💡 **Pro-Tip (`pre-commit`)**: Keep `uv.lock` automatically in sync:

```yaml
- repo: https://github.com/astral-sh/uv-pre-commit
  rev: 0.12.5
  hooks:
    - id: uv-lock
```

<!--
Presenter Note:
To wrap up part one: uv.lock gives us total reproducibility and supply chain security by locking SHA256 hashes.
In CI, we run uv sync --locked to make sure uv.lock matches pyproject.toml. In production or Docker, we run uv sync --frozen for blazing-fast installs.
Now that our environment is locked and fully reproducible, let's see how we can make Odoo packaging even simpler!
-->

---

<!-- _class: lead -->
# **A Dedicated Build Backend for Odoo**
### Streamlining Odoo packaging with `hatch-odoo`

<!--
That brings us to section two!
So far, we used Astral's standard uv_build backend, but it forced us into nested folders (src/odoo/addons/) and manual dependency duplication.
Let's see how a dedicated Odoo build backend like hatch-odoo solves this!
-->

---

# **What is a Build Backend? (`hatch` & `hatch-odoo`)**

### Definitions & Ecosystem

- **Build Backend** (PEP 517 / PEP 518): The tool responsible for compiling source code into standard Python distribution packages (`.whl` wheels or `.tar.gz` sdist). Examples: `setuptools`, `flit`, `hatchling`, `uv_build`.
- **Hatch / Hatchling**: A modern, highly extensible Python build backend that supports custom build and metadata hooks.
- **`hatch-odoo`**: A specialized Hatch plugin created by **Stéphane Bidoul** (ACSONE) tailored specifically for Odoo projects.

### What value does `hatch-odoo` add?
1. **Flexible `addons_dirs`**: Maps any project directory into `odoo.addons` without rigid `src/odoo/addons/` folder constraints.
2. **Dynamic Dependencies**: Automatically resolves PyPI requirements directly from `__manifest__.py`.

<!--
Before we look at hatch-odoo, what is a build backend?
In Python, the build backend compiles your source files into standard wheels.
hatch-odoo is a specialized build backend created by Stéphane Bidoul at ACSONE. It gives us two huge features: flexible addon directories and dynamic dependency resolution.
-->

---

# **Flexible Addons Paths with `hatch-odoo`**

### Configure `hatch-odoo` build backend in `pyproject.toml`

```toml
[build-system]
requires = ["hatchling", "hatch-odoo"]
build-backend = "hatchling.build"

# Enable hatch-odoo build hook for local addons directory
[tool.hatch.build.hooks.odoo-addons-dirs]

[tool.hatch-odoo]
addons_dirs = ["src/odoo/addons"]
```

### Why `addons_dirs` is a game changer
- Standard `uv_build` namespace requires a strict nested folder layout: `src/odoo/addons/<addon_name>`.
- **`hatch-odoo`'s `addons_dirs`** allows referencing **any** directory (e.g. `odoo/addons`, `custom_addons`, `third_party/addons`) or multiple directories without deep nesting!

<!--
Presenter Note: Step 4 (git checkout step-4)
With standard uv_build, we had to use a strict folder layout (src/odoo/addons/).
With hatch-odoo, we set addons_dirs = ["src/odoo/addons"]. You can point to custom_addons, third_party, or multiple directories without deep folder nesting.
-->

---

# **Deep Dive: Under the Hood of `hatch-odoo`**

### How `hatch-odoo` connects `addons_dirs` to Python

- **Relevant Python Standards**:
  - **PEP 420**: Implicit Namespace Packages (`odoo.addons`).
  - **PEP 517 / PEP 660**: Build backends & editable installs.
  - **Python `site` module & `.pth` files**: Standard Python mechanism to inject paths into `sys.path` on startup.

### Mechanics in Editable Mode

1. **Symlink Tree**: In editable dev mode, `hatch-odoo` creates `build/__editable_odoo_addons__/odoo/addons/` with symlinks to each installable addon in `addons_dirs`. *(Non-installable addons are skipped)*.
2. **`.pth` File**: Injects `<project>_editable_odoo_addons.pth` into `.venv/.../site-packages/` pointing to `build/__editable_odoo_addons__`.
3. ⚡ **Zero-Copy & PEP 420**: Python's `site` module reads the `.pth` file, adding `build/__editable_odoo_addons__` to `sys.path`. PEP 420 resolves `odoo.addons.<addon>` via symlinks directly to source code!

> *Production Wheel Mode (`uv build`)*: No symlinks are used. Addons are packaged directly into `odoo/addons/` inside the `.whl` wheel archive.

<!--
Presenter Note:
Under the hood, hatch-odoo creates a .pth file in .venv pointing to a build folder filled with symlinks to all your installable addons.
Python reads the .pth file on startup and exposes them directly inside odoo.addons.
And when you build a production wheel with uv build, it packages everything neatly without symlinks.
-->

---

# **Dynamic Dependencies with `hatch-odoo`**

### Declare dependencies in `src/odoo/addons/ocadays_2026/__manifest__.py`

```python
"depends": ["web", "mis_builder", "partner_firstname"]
```

### Enable dynamic resolution in `pyproject.toml`

```toml
[project]
dynamic = ["dependencies"]

[tool.hatch.metadata.hooks.odoo-addons-dependencies]
[tool.hatch-odoo]
odoo_version_override = "19.0"
dependencies = [
    "odoo", "lxml>=5.2.1", "lxml-html-clean", "Werkzeug==3.0.1", "PyPDF==5.4.0", "freezegun",
]
```

> 🪄 **How it works**: `hatch-odoo` reads manifest `depends`, resolves PyPI packages (`odoo-addon-partner-firstname`), and locks them automatically on `uv sync`!
> ⚠️ *Trade-off: `uv add` is no longer used.*

<!--
Presenter Note: Step 5 (git checkout step-5)
Even better: hatch-odoo supports dynamic dependencies!
Notice how in pyproject.toml, we no longer list any odoo-addon-* packages explicitly!
By setting dynamic = ["dependencies"], hatch-odoo reads the depends field in __manifest__.py and automatically maps them to PyPI packages like odoo-addon-partner-firstname on uv sync.
[joke] You only declare dependencies in one single place—no more keeping __manifest__.py and pyproject.toml in sync manually!
-->

---

<!-- _class: lead -->
# **Unreleased PR Dependencies**

<!--
Now, let's address a real-world problem every Odoo developer faces: working with unmerged OCA Pull Requests!
-->
---

# **Working with Unmerged OCA Pull Requests**

### Real-world scenario: Need an unmerged fix on `OCA/mis-builder#827`

```toml
# pyproject.toml
[tool.uv.sources]
odoo = { git = "https://github.com/OCA/OCB.git", branch = "19.0" }
odoo-addon-mis-builder = { git = "https://github.com/OCA/mis-builder.git", rev = "refs/pull/827/head", subdirectory = "mis_builder" }
```

### Run `uv sync`

- `uv` overrides the PyPI wheel with the Git PR reference.
- Locks the exact target Git commit in `uv.lock`.

> 💡 **Pro-Tip (Transitive Dependencies)**: For `[tool.uv.sources]` to apply to a *transitive* (sub-dependency) module, it must be explicitly declared as a direct requirement!

<!--
Presenter Note: Step 6 (git checkout step-6)
Very often, a bug fix or new feature is in an open OCA PR that hasn't been merged yet.
With uv, we can add a source override in [tool.uv.sources] pointing directly to refs/pull/827/head.
uv sync pulls the PR code directly from Git and locks the exact commit SHA in uv.lock.
-->

---

# **PR Dependency Architecture**


### How `uv` links PRs to `uv.lock`

- **Declaration**: `pyproject.toml` targets `refs/pull/666/head`.
- **Locking**: `uv.lock` captures the exact commit SHA (e.g., `a1b2c3d4e`).
- **Resolution**: `uv sync` fetches `a1b2c3d` from the upstream GitHub repo.

![bg 75%](images/diagram_pr_dependency-wihout-uvault.svg)

<!--
Presenter Note:
Here is how uv handles PR dependencies: pyproject.toml defines the target PR reference, and uv.lock captures the exact git commit SHA.
uv sync then fetches that specific commit to build the environment.
-->

---

# **Local Editable Mode for OCA Contributions**

### Fix a bug or contribute to the OCA PR locally

```bash
# Clone the PR repository locally into .src/ (added to .gitignore)
git clone https://github.com/OCA/mis-builder .src/mis-builder
```

### Update `pyproject.toml` to editable source:

```toml
[tool.uv.sources]
odoo-addon-mis-builder = { path = ".src/mis-builder/mis_builder", editable = true }
```

> ⚡ **Instant feedback**: Any code modification in `.src/mis-builder/mis_builder` is instantly live in Odoo without re-installing nor hacking the addons path!

<!--
Presenter Note: Step 7 (git checkout step-7)
If you want to edit that OCA PR locally, you clone the repository into .src/ and set path = ".src/..." with editable = true.
Any changes you make locally are reflected immediately in Odoo without reinstalling! No need to hack the addons path!
-->

---

# **The Hidden Hazard: Git Garbage Collection 💣**


### Upstream Force-Push / Rebase Hazard!

![width:700px](images/diagram_pr_dependency-wihout-uvault-broken.svg)

If the PR author rebases or force-pushes, the old commit hash is **garbage collected**:


```bash
$ uv sync
  ...
  Updating https://github.com/OCA/repo.git (refs/pull/666/head)
  × Failed to download and build `package @ git+https://github.com/OCA/repo.git@a1b2c3d4e#subdirectory=module`
  ├─▶ Git operation failed
  ├─▶ failed to fetch into: ~/.cache/uv/git-v0/a1/b2c3d4e
  ├─▶ failed to fetch commit `a1b2c3d4e`
  ╰─▶ process didn't exit successfully: `git fetch --force --update-head-ok 'https://github.com/OCA/repo.git'
      '+a1b2c3d4e:refs/commit/a1b2c3d4e'` (exit status: 128)
      --- stderr
      fatal : distant error : upload-pack: not our ref a1b2c3d4e
```

<br/>

> 💥 **CI & Production builds break instantly!**


<!--
Presenter Note: Step 8 (git checkout step-8)
Now, here is the trap!
[joke] Raise your hand if a production deployment broke at 3 AM because an external PR author force-pushed or rebased their branch!
If the PR author rebases, the old commit hash is deleted by GitHub's garbage collector.
Your CI build running uv sync --locked fails with the fatal error: "upload-pack: not our ref"!
-->

---

# **Why Direct Git URLs are a Time Bomb 💣**

> 1. **Upstream Force-Push / Rebase**: Targeted commit disappears ➡️ CI/Prod builds break instantly.
> 2. **Closed PR or Deleted Branch**: Dependency becomes unavailable.
> 3. **Lack of Immutability**: Pointing to branch names exposes your project to unvetted changes.

<br/><br/>
<br/><br/>

## **How can we preserve these PR commits so our builds never break ❓**

<!--
To summarize: direct Git URLs in pyproject.toml are a ticking time bomb.
First, force-pushes delete commits. Second, PRs can be closed or branches deleted. Third, branch references are not immutable.
So how can we preserve these PR commits under our own control so our builds never break?
-->

---

# **The Solution: Controlled Remote Repository - Vault repo**

### Preserve PR commits in an organization-controlled Git Vault

- **Freeze & Mirror**: Push PR commit `a1b2c3d4e` as an immutable tag (`pjt-a1b2c3d4e`) to your own Vault repo.
- **Independence**: Builds depend on your Vault repository, rendering them immune to upstream deletions.

<div align="center">

![width:720px](images/diagram_pr_dependency-with-uvault.svg)

</div>

<!--
Presenter Note:
The solution is to "vault" unmerged PR commits!
Instead of pointing to a volatile PR branch on GitHub, we fetch the PR commit and push an immutable tag (like pjt-<sha>) to a Vault Git repository owned by our organization.
Now, our build depends entirely on our own repository!
-->

---

# **Garbage Collection Immunity**

### Surviving upstream force-pushes & rebases

- **Upstream Rebase**: Upstream author force-pushes commit `f5g6h7i8j`. Old commit `a1b2c3d4e` is GC'd upstream.
- **Zero CI Breakage**: Your Vault repository retains `a1b2c3d4e` via tag `pjt-a1b2c3d4e`. Builds stay 100% reproducible.
- **Updating**: A new tag (`pjt-f5g6h7i8j`) is pushed to your Vault when you choose to update.

<div align="center">

![width:700px](images/diagram_pr_dependency-with-uvault-garbadged-former-commit.svg)

</div>

<!--
Presenter Note:
Even if the PR author force-pushes a brand-new commit, your Vault repository still holds the original commit under your tag.
Your CI and production builds continue running smoothly without breaking!
-->

---

# **The Catch: Doing This Manually is Painful 🤯**

> **Manual Vaulting Requires High Rigor & Heavy Effort:**
> 
> 1. `git fetch` raw PR refs for every unmerged dependency.
> 2. Create tags & push them to your private Vault repository.
> 3. Update `pyproject.toml` and `uv.lock` sources manually.
> 4. Track upstream PR status (merged, closed, updated) by hand.

### 💡 **Doing this manually is tedious and error-prone... What if a tool did it for you?**

<!--
Presenter Note:
[joke] Vaulting commits manually sounds great on paper, but doing it by hand for 10 or 20 PRs is a recipe for losing your mind.
You have to manually fetch refs, create tags, push to remote vaults, update pyproject.toml, and check PR statuses.
That's why we created a tool to automate all of this: uvault.
-->

---

<!-- _class: lead -->
# **3. DEEP DIVE: `uvault` 🛡️**
### A dedicated VCS workflow tool for `uv`

<!--
Let's begin part four: a deep dive into uvault!
-->

---

# **What is `uvault`?**
<br/>
<br/>

### A tool to manage your VCS dependencies in your `uv` development environment

<br/>

**Standalone CLI** executed via `uvx` (`uvx --with uvault[github] uvault <command>`).
  *(Note: `uvx` is `uv`'s tool runner—like `npx` or `pipx`—executing CLI tools in isolated ephemeral environments without installing them into your project)*
<br/>

Inspired by **`pip-preserve-requirements`** (by Stéphane Bidoul).

<!--
uvault is a CLI tool executed via uvx.
What is uvx compared to uv? While uv manages your project and virtual environments, uvx (short for 'uv tool run', similar to npx in Node or pipx in Python) executes standalone CLI tools in isolated, temporary environments on the fly. You don't need to install uvault into your project's .venv!

Inspired by Stéphane Bidoul's pip-preserve-requirements, uvault manages your VCS dependencies by:
1. Auto-vaulting PR commits with immutable tags into your organization's Vault repo.
2. Easily switching dependencies to local editable mode.
3. Monitoring upstream PR statuses (merged, closed, or force-pushed).
4. Tagging dependencies for production releases.
-->

---

# **Declare Intent (`uvault add`)**

Instead of manually editing `pyproject.toml`, declare a VCS dependency **intention**:

```bash
uvx uvault add odoo-addon-web_switch_company_favorite \
  https://github.com/OCA/multi-company \
  --pr 1020 \
  --subdirectory web_switch_company_favorite
```

### Result in `pyproject.toml`:

```toml
[tool.uvault.sources]
odoo-addon-web_switch_company_favorite = {git = "https://github.com/OCA/multi-company", rev = "refs/pull/1020/head", subdirectory = "web_switch_company_favorite"}
```

> ℹ️ *Note: `uvault add` configures the intention in `[tool.uvault.sources]`. It does not touch `[tool.uv.sources]` or lockfile yet.*

<!--
Presenter Note: Step 1 (git checkout step-uvault-1)
Instead of manually editing pyproject.toml, you run uvault add with the package name, repo URL, PR number, and subdirectory.
uvault saves your intention under [tool.uvault.sources].
-->

---
# **Configure your project before the first sync**
<br/>

```toml
[tool.uvault]
tag_prefix = "ocadays26"
tag_template = "{tag_prefix}-{sha}"
dev_directory = ".src/"

# VCS Vault Configuration
[[tool.uvault.vcs_vaults]]
provider = "github.com"
owner = "apycod"
default = true
```
<br/><br/>
<br/>


More config options available in the documentation: [https://uvault.apycod.com/reference/#pyprojecttoml-configuration](
https://uvault.apycod.com/reference/#pyprojecttoml-configuration)

<!--
Presenter Note:
Before syncing, we configure project settings in pyproject.toml, such as the tag prefix (e.g. ocadays26) and our Vault repository owner.
-->

---
# **Vault & Freeze (`uvault sync`)**

### Run synchronization

```bash
uvx uvault[github] sync
```

### What happens under the hood? ⚙️

1. **Fetches** the exact commit of PR `#1020` (`refs/pull/1020/head`).
2. **Auto-forks** via GitHub API if repo doesn't exist in your Vault org yet.
3. **Pushes immutable tag** (`pjt-<sha>`) to your Vault repo (`apycod/multi-company`).
4. **Updates `[tool.uv.sources]`** with the secure Vault reference:

```toml
[tool.uv.sources]
odoo-addon-web_switch_company_favorite = {git = "https://github.com/apycod/multi-company.git", tag = "ocadays26-d9309...", subdirectory = "web_switch_company_favorite"}
```

> 🔑 *Note: Auto-forking requires `uvault[github]` and `[github] token` in `~/.config/uvault/config.toml`.*

### Don't forget to update `uv.lock` file after `uvault sync`

```bash
uv sync
```

<!--
Presenter Note: Step 2 (git checkout step-2-uvault)
Now we run uvx uvault sync.
Under the hood, uvault fetches the PR commit, automatically forks the repo into your Vault organization if needed, pushes an immutable tag, and updates [tool.uv.sources]!
Then, a simple uv sync updates uv.lock.
-->

---

# **Monitor PR Status (`uvault status`)**

### Stay in control of external dependencies

```bash
$ uvx uvault[github] status --format inline --sort-by status
VCS Metadata:
  🔵 [PullRequestStatus.ACTIVE]  odoo                                              (RefType.BRANCH 17.0, à jour)
  🔵 [PullRequestStatus.ACTIVE]  pytest-odoo                                       (RefType.BRANCH master, à jour)
  🔴 [PullRequestStatus.CLOSED]  odoo-addon-account-invoice-constraint-chronology  (RefType.PR 1889, à jour, labels: approved,ready to merge,stale)
  🔴 [PullRequestStatus.CLOSED]  odoo-addon-account-move-name-sequence             (RefType.PR 1949, à jour, labels: stale)
  🟢 [PullRequestStatus.MERGED]  odoo-addon-account-move-cutoff                    (RefType.PR 340, à jour, labels: approved,ready to merge,merged 🎉,stale)
  🟢 [PullRequestStatus.MERGED]  odoo-addon-hr-attendance-modification-tracking    (RefType.PR 196, +51 commits (Force-Push detecté!), labels: approved,ready to merge,merged 🎉)
  🟢 [PullRequestStatus.MERGED]  odoo-addon-hr-holidays-public                     (RefType.PR 225, à jour, labels: merged 🎉,stale)
  🟡 [PullRequestStatus.OPEN]    odoo-addon-ai-oca-mcp                             (RefType.PR 85, +1 commits)
  🟡 [PullRequestStatus.OPEN]    odoo-addon-ai-tool                                (RefType.PR 84, à jour)
  🟡 [PullRequestStatus.OPEN]    odoo-addon-hr-attendance-overtime                 (RefType.PR 234, à jour, labels: approved,ready to merge)
  🟡 [PullRequestStatus.OPEN]    odoo-addon-hr-attendance-validation               (RefType.PR 221, +72 commits (Force-Push detecté!))
  🟡 [PullRequestStatus.OPEN]    odoo-addon-project-consumable                     (RefType.PR 1569, à jour, labels: needs review,migration)
  ⚪ [PullRequestStatus.UNKNOWN] mattermostautodriver                              (RefType.TAG 2.3.0, à jour)
```

### Diagnostics provided by `uvault status`:

- 🟢 **PR Merged**: The OCA PR was merged upstream! Switch back to standard PyPI release.
- 🔴 **PR Closed**: PR closed without merging.
- ⚡ **New Remote Commits**: New commits pushed to PR (`uvault sync --update` to fetch).
- ⚠️ **Orphaned Commit**: Upstream PR was rebased/force-pushed! Your Vault retains the old commit: **your prod build remains 100% functional**!

> 🔑 *Requires `uvault[github]` & `[github] token` in `~/.config/uvault/config.toml` to query GitHub API.*

<!--
Presenter Note: Step 3 (git checkout step-3-uvault)
How do we track upstream changes? With uvault status!
It queries the GitHub API and shows clear traffic lights:
[joke] Green means the PR was finally merged—time to celebrate and switch back to PyPI!
Red means closed, yellow means open, and warnings alert you if a force-push happened upstream.
-->

---

# **Local Dev (`uvault develop`)**

Need to modify the OCA PR or add a feature locally, let's fix a bug in partner_firstname

```bash
# add uvault reference on OCA 19.0 branch (deduce github repository from existing package localy or pypi metadata)
uvx uvault add odoo-addon-partner-firstname --branch 19.0 --subdirectory partner_firstname
uvx uvault develop odoo-addon-partner-firstname 19.0-partner_firstname-fix
```

### Automatic actions performed by `uvault develop`:

1. **Clones** repo to `.src/odoo-addon-partner-firstname` & configures remotes (`origin`, `vault`, + custom remotes from `config.toml` like `petrus-v`).
2. **Switches `pyproject.toml`** to local `editable` mode (`path = "./.src/..."`).
3. Run **`uv sync`**: local changes are instantly live in Odoo!

> 💡 *Pro-Tip*: Custom remotes in `~/.config/uvault/config.toml` under `[remotes]` so you can `git push` to your fork effortlessly!
<br/>

> **Safety Net (`uvault-check`)**: Prevent committing local editables with `uvault`:

```yaml
- repo: https://github.com/petrus-v/uvault
  rev: v0.6.1
  hooks:
    - id: uvault-check   # Reverts editables & restores Vault sources before uv-lock pre-commit hook!
```

<!--
Presenter Note: Step 4 (git checkout step-4-uvault)
Need to fix a bug in an OCA addon locally?
Run uvault develop <package> <branch>. It automatically clones the repository into .src/, sets up git remotes, and switches pyproject.toml to editable mode.
[joke] And to prevent committing local editable paths by accident, we provide a uvault-check pre-commit hook that acts as a safety net!
-->

---

# **Freeze for Production (`uvault release`)**

### Ensure 100% immutable builds for production deployments

```bash
# 1. Freeze all vaulted dependencies to the current release version
uvx uvault release

# 2. Production deployment (in Docker / CI / Server)
uv sync --frozen --no-dev
```

### What happens under the hood?

1. **Reads project version** from `pyproject.toml` (e.g., `19.0.1.0.0`).
2. **Tags Vault repositories** with release tags (e.g., `ocadays26-19.0.1.0.0`).
3. **Updates `[tool.uv.sources]`** to point to these new release tags.
4. **Deploy with `uv sync --frozen --no-dev`**: 100% deterministic, zero resolution overhead, excludes dev tools.

> 💡 *Integrated directly with release tools like `bump-my-version` via pre-commit hooks.*

<!--
Presenter Note:
When it's time for a production release, run uvault release.
It tags all vaulted dependencies with your release version tag (e.g. ocadays26-19.0.1.0.0).
Then, in CI or Docker, uv sync --frozen --no-dev gives you a 100% deterministic, tamper-proof build.
-->

---

# **User Configuration (`~/.config/uvault/config.toml`)**

### Machine-level configuration for automation

```toml
# ~/.config/uvault/config.toml
[remotes]
petrus-v = "ssh://git@github.com/petrus-v"  # Custom remotes added on `uvault develop`

[github]
token = "github_pat_11A...xxx"              # GitHub PAT for API queries & auto-forking
```

### Key usages:
- **`[github] token`** *(requires `uvault[github]`)*:
  - **`uvault status`**: Queries GitHub API (PR states, labels, force-push detection) without rate limits.
  - **`uvault sync`**: Enables auto-forking missing repositories into your Vault organization.
- **`[remotes]`**: Used by **`uvault develop`** to auto-add your personal Git remotes when setting up `./.src/` clones for easy pushing.

<!--
Presenter Note:
Machine-level configuration sits in ~/.config/uvault/config.toml.
Your GitHub token enables status checks and automatic repo forking.
The [remotes] section lets uvault develop automatically add your personal GitHub fork so you can git push right away.
-->

---
# **My Release Lifecycle**

### **PEP 440**

`1.0.1.dev0` (dev) < `1.0.1` (final release) < `1.0.2.dev0`.

### Automation with `bump-my-version`:

![width:800px](images/uvault-release-workflow.svg)

```bash
# 1. In dev: pyproject.toml version = 1.2.0.dev0
# 2. Final Production Release tag:
uvx bump-my-version bump release
# -> bump-my-version pre-commit hook runs `uvault release` & freezes tag v1.2.0!
# 3. Next Dev cycle:
uvx bump-my-version bump minor --no-tag  # -> moves to 1.3.0.dev0
```



Example of bump-my-version configuration that triggers uvault release: [https://uvault.apycod.com/how-to/#recommended-bump-my-version-configuration](https://uvault.apycod.com/how-to/#recommended-bump-my-version-configuration)


<!--
Presenter Note: Step 5 (git checkout step-5-uvault)
uvault integrates seamlessly with bump-my-version.
When you bump your release version, a pre-commit hook automatically runs uvault release and freezes immutable release tags for deployment.
-->

---

# **`uvault` Future Evolutions & Roadmap**

### Ideas & upcoming improvements

- **`gitaggregator` Integration (Multi-PR per package)**:
  - Aggregate multiple PRs into a single package dependency before vaulting (when an Odoo module relies on multiple unmerged PRs).
- **PR Diff Inspection (`uvault status`)**:
  - Preview git diff directly before deciding to run `uvault sync --update`.
- **Multi-Vault Support**:
  - Explicitly map specific packages to dedicated Vault organizations/repos.
- **Multi-Forge Battle-Testing**:
  - Battle-test GitLab integration (backend already structured).
  - Add support for Gitea / Forgejo.
- ⚡ **Auto-`uv sync` Option** (To Be Determined):
  - Optional flag to trigger `uv sync` immediately after `uvault sync` (currently decoupled for flexibility).

<!--
Presenter Note:
Looking ahead, the roadmap for uvault includes:
- Integrating gitaggregator to merge multiple PRs for a single addon.
- Showing PR diff previews in uvault status.
- Expanding multi-forge support for GitLab, Gitea, and Forgejo.
- And optional auto-sync flags.
-->

---

# **Resources & Links 🔗**

- **`uv` by Astral**: [github.com/astral-sh/uv](https://github.com/astral-sh/uv)
- **`hatch-odoo` & `whool`**:
  - [github.com/acsone/hatch-odoo](https://github.com/acsone/hatch-odoo)
  - [github.com/sbidoul/whool](https://github.com/sbidoul/whool)
- **`uvault`**:
  - GitHub Repo: [github.com/petrus-v/uvault](https://github.com/petrus-v/uvault)
  - Documentation: [uvault.apycod.com](https://uvault.apycod.com)
- **pip-preserve-requirements**: Original uvault inspiration by Stéphane Bidoul
  - [github.com/sbidoul/pip-preserve-requirements](https://github.com/sbidoul/pip-preserve-requirements)
- **This presentation**
  - https://odoo-dev.apycod.com/
  - repo: petrus-v/odoo-dev-presentation
    - [`main` branch](https://github.com/petrus-v/odoo-dev-presentation)   
    - [`example` branch](https://github.com/petrus-v/odoo-dev-presentation/tree/example)   

<!--
Here are all the key links and resources.
Check out uv by Astral, hatch-odoo and whool by Stéphane Bidoul, and uvault at github.com/petrus-v/uvault.
-->

---

<!-- _class: lead -->


![bg contain](./images/sponsor.png)

<!--
Thank you very much for your time!
A huge thank you to our sponsors for making OCA Days 2026 possible.
I'm now happy to take any questions!
-->
