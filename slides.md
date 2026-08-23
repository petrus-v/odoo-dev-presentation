---
marp: true
theme: gaia
_class: lead
paginate: true
backgroundColor: #ffffff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
style: |
  :root {
    --color-background: #ffffff;
    --color-foreground: #2b3a4a;
    --color-highlight: #0288d1;
    --color-dimmed: #6c757d;
  }
  section:not(.lead) {
    font-size: 100%;
    padding: 25px 150px 75px 200px;
    color: #2b3a4a;
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
    color: #0288d1;
  }
  h2 {
    font-size: 1.25em;
    color: #2b3a4a;
  }
  h3 {
    font-size: 1.05em;
    color: #0288d1;
  }
  p, ul, ol {
    line-height: 1.35;
    color: #2b3a4a;
  }
  strong {
    color: #0288d1;
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
    background-color: #eef2f7;
    color: #d63384;
    padding: 0.1em 0.3em;
    border-radius: 4px;
    font-weight: bold;
  }
  blockquote {
    background: #f0f4f8;
    border-left: 6px solid #0288d1;
    padding: 8px 14px;
    font-size: 95%;
    color: #2b3a4a;
    text-align: left;
  }
  table {
    font-size: 85%;
    width: 100%;
    color: #2b3a4a;
  }
  th {
    background-color: #eef2f7;
    color: #0288d1;
  }
---

# **My Odoo Dev Environment: From Dev to Production**

### With `uv`, `hatch-odoo` and **`uvault`** 🚀

**OCA Days 2026** — Pierre Verkest (APYCOD)
GitHub: @petrus-v

<!--
Hello everyone, and welcome to this session at OCA Days 2026.
My name is Pierre Verkest from APYCOD, and today I'm excited to talk about how we can make Odoo projects as Pythonic as possible—simplifying onboarding and engaging Python developers—while taking your project smoothly from local dev all the way to production using tools like uv, hatch-odoo, and a dedicated new tool called uvault.
-->

---

# **About**

- **Pierre Verkest** — Odoo Developer @ APYCOD
- **OCA Contributor** & creator of `uvault`
- **Context**: Historically, Odoo struggled with standard Python tooling (`addons_path`). Today, it integrates seamlessly (`uv`, `pyproject.toml`)!

<!--
To introduce myself: I'm Pierre Verkest from APYCOD, Odoo developer, OCA contributor, and creator of uvault.
Historically, Odoo struggled to integrate smoothly with standard Python tooling due to legacy mechanisms like addons_path. Today, thanks to uv, pyproject.toml, whool, and hatch-odoo, Odoo fits naturally into the modern Python ecosystem!
-->

---

# **Agenda**

1. 🐍 **Odoo as a Standard Python Project**: Modern tooling (`uv`, PyPI, `whool`, `hatch-odoo`)
2. 💣 **The Friction**: Unreleased PR dependencies & Git Garbage Collection hazards
3. 🛡️ **DEEP DIVE `uvault`**: Vaulting, local editable dev & release lifecycle

<!--
Here is our agenda today:
1. Managing an Odoo project like any standard Python project using uv, PyPI, hatch-odoo, and editable local sources.
2. The real-world friction of unreleased PR dependencies and the hidden hazard of Git Garbage Collection on forced-pushed commits.
3. A deep dive into uvault for vaulting PRs, managing local editable workflows, and release lifecycles.
-->

---

<!-- _class: lead -->
# **Odoo as a Standard Python Project**

<!--
Let's see how an Odoo project can be initialized, configured, and managed like any standard Python project.
-->

---

# **Project Initialization (`uv init`)**

### Repository setup

```bash
uv init ocadays-2026-odoo-dev
cd ocadays-2026-odoo-dev
```

### Repository layout 📂

```text
ocadays-2026-odoo-dev/
├── .python-version      # Target Python version (e.g. 3.14)
├── pyproject.toml       # Project metadata & dependencies
└── README.md
```

> ⚡ **Result**: Your project root is initialized with standard Python configuration files!

<!--
Presenter Note: Step 0 (git checkout step-0)
In milliseconds, uv initializes a clean Python repository with pyproject.toml and .python-version.
-->

---

# **Installing Odoo Core & Custom Addon**

### Custom addon: `odoo/addons/ocadays_2026/__manifest__.py`

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
We create our custom Odoo module inside odoo/addons/ocadays_2026 and declare Odoo 19.0 from OCB git repository in pyproject.toml.
-->

---

# **Running Odoo with `uv run` 🚀**

### Auto-synced virtual environment

```bash
# Sync environment & build virtualenv automatically
uv sync

# Run Odoo CLI with manual --addons-path for local custom module
uv run odoo \
  --addons-path=odoo/addons \
  -d ocadays2026 -i ocadays_2026 \
  --stop-after-init
```

> 💡 **No `source .venv/bin/activate` needed!** `uv run` syncs and runs inside `.venv` seamlessly.
> ⚠️ *Note: `--addons-path=odoo/addons` is still required here because local addons are not packaged into `site-packages` yet!*

<!--
Presenter Note: Step 1 (continued)
Notice how Odoo starts immediately with uv run. uv handles virtualenv creation and package installation transparently.
At this stage, we still need --addons-path=odoo/addons for local modules. Next, let's see how hatch-odoo eliminates this!
-->

---

# **Adding OCA Dependencies (PyPI & `whool`)**

### Update manifest `odoo/addons/ocadays_2026/__manifest__.py`

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

<!--
Presenter Note: Step 2 (git checkout step-2)
We add an OCA dependency, mis_builder.
We add "mis_builder" to our manifest depends and "odoo-addon-mis-builder" to pyproject.toml.
uv sync fetches the wheel from PyPI alongside all its transitive OCA dependencies!
-->

---

# **Under the Hood: PyPI & `whool` 📦**

### Did you know EVERY OCA module is published on PyPI?

- Huge thanks to **Stéphane Bidoul** for the **`whool`** build backend!
- OCA repositories automatically build and publish standard Python wheels on **pypi.org**.
- Standard PyPI naming convention: `odoo-addon-<module_name>`
  - `mis_builder` ➡️ `odoo-addon-mis-builder`
  - `partner_firstname` ➡️ `odoo-addon-partner-firstname`

```bash
# Adding any OCA module is standard Python package management!
uv add odoo-addon-account-financial-report
```

<!--
Thanks to whool by Stéphane Bidoul, every OCA module builds standard Python wheels on PyPI under the naming scheme odoo-addon-<module>.
-->

---

# **IDE Pro-Tip: Navigating `site-packages` 💡**

### Everything is installed in `.venv/lib/python3.x/site-packages/`

- All OCA modules and Odoo core reside inside your `.venv`.
- **Recommended practice**: Add `.venv/.../site-packages/odoo/addons` to your IDE workspace (VSCodium / VS Code / PyCharm).

### Key developer benefits:
- 🔍 **Global Code Search**: Search classes, views, and methods across all OCA addons.
- 🐞 **Seamless Debugging**: Set breakpoints directly inside any third-party OCA module.
- 🎯 **Go-to-Definition**: Fast navigation to inherited models and methods.

<!--
Presenter Note: (In VSCodium demo, show adding site-packages/odoo/addons to workspace folders)
Explain how adding site-packages/odoo/addons to the workspace gives full code search, autocompletion, and breakpoint capabilities across all installed OCA modules!
-->

---

# **Eliminating `addons-path` with `hatch-odoo`**

### Enable `hatch-odoo` build backend

```toml
# pyproject.toml
[build-system]
requires = ["hatchling", "hatch-odoo"]
build-backend = "hatchling.build"

# Enable hatch-odoo build hook for local addons directory
[tool.hatch.build.hooks.odoo-addons-dirs]

[tool.hatch-odoo]
addons_dirs = ["odoo/addons"]
```

### How `hatch-odoo` solves `addons-path` 🎉
- Dynamically extends the `odoo.addons` Python namespace package.
- **`--addons-path` is now completely eliminated!**

```bash
# No more --addons-path=... parameter required!
uv run odoo -d ocadays2026 -i ocadays_2026 --stop-after-init
```

<!--
Presenter Note: Step 3 (git checkout step-3)
hatch-odoo leverages Python's namespace package mechanism (via .pth hooks in site-packages) to inject local addons into odoo.addons. No more endless addons-path lines in odoo.conf!
-->

---

# **Dynamic Dependencies with `hatch-odoo`**

### Declare dependencies in `__manifest__.py`

```python
# odoo/addons/ocadays_2026/__manifest__.py
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
> ⚠️ *Trade-off: `uv add` is no longer used; dependencies are single-sourced in `__manifest__.py`.*

<!--
Presenter Note: Step 4 (git checkout step-4)
Dynamic dependencies single-source requirements in __manifest__.py. hatch-odoo maps them to PyPI wheels automatically. Mention that uv add is replaced by manifest edits.
-->

---

# **Reproducibility & Security: `uv.lock`**

### The role of `uv.lock`

- **Cross-platform lockfile** (multi-OS, multi-Python versions).
- Stores the **exact** version, Git revision, and SHA256 hash of every dependency (core Odoo, OCA modules, C/Python libs).
- **Supply Chain Protection**: Verifies SHA256 package hashes at install time to prevent tampered artifacts or PyPI republishing attacks.

> 💡 **Pro-Tip (`pre-commit`)**: Auto-sync and ensure consistency of `uv.lock` synced with your pyproject.toml

```yaml
- repo: https://github.com/astral-sh/uv-pre-commit
  rev: 0.12.5
  hooks:
    - id: uv-lock
```

> 🎯 **Guarantee**: Local Dev == CI == Staging == Production (Deterministic & Tamper-proof).

<!--
uv.lock guarantees absolute reproducibility and supply chain security across all environments.
By recording and checking SHA256 hashes during `uv sync`, uv prevents tampered or compromised packages from being installed.
Key commands: `uv sync` (sync venv with lockfile), `uv lock` (generate/update), `uv tree` (inspect graph).
However, if a Git dependency commit vanishes upstream, lockfile reproducibility alone cannot restore the missing commit!
-->

---

<!-- _class: lead -->
# **The Friction: Volatile PRs & Git Garbage Collection**

<!--
Now let's examine the real-world friction every Odoo team faces when depending on unmerged PRs or temporary fork branches.
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
Presenter Note: Step 5 (git checkout step-5)
In real projects, we often need unmerged bugfixes or features from open OCA PRs.
By adding a source override in tool.uv.sources pointing to refs/pull/827/head, uv pulls directly from the Git PR branch.
Tip: To override a transitive sub-dependency with a Git PR, make sure to list it as a direct dependency so tool.uv.sources resolves it!
-->

---

# **PR Dependency Architecture**


### How `uv` links PRs to `uv.lock`

- **Declaration**: `pyproject.toml` targets `refs/pull/666/head`.
- **Locking**: `uv.lock` captures the exact commit SHA (e.g., `a1b2c3d4e`).
- **Resolution**: `uv sync` fetches `a1b2c3d` from the upstream GitHub repo.

![bg 75%](images/diagram_pr_dependency-wihout-uvault.svg)

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

> ⚡ **Instant feedback**: Any code modification in `.src/mis-builder/mis_builder` is instantly live in Odoo without re-installing!

<!--
Presenter Note: Step 6 (git checkout step-6)
When you need to work on the PR locally, switch the source to path = ".src/..." with editable = true. Your local edits are picked up immediately by Odoo.
-->

---

# **The Hidden Hazard: Git Garbage Collection 💣**

![bg right:48% contain](images/diagram_gc_hazard.svg)

### Upstream Force-Push / Rebase Hazard!

If the PR author rebases or force-pushes, the old commit hash is **garbage collected**:

```text
$ uv sync --locked
× Failed to download and build `odoo-addon-mis-builder @ git+...`
  ├─▶ Git operation failed: failed to fetch commit `88d87101821126a62aa3...`
  ╰─▶ fatal: erreur distante : upload-pack: not our ref 88d87101821126a62aa3...
```

> 💥 **CI & Production builds break instantly!**

<!--
Presenter Note: Step 7 (git checkout step-7)
Back to PR reference. But here lies the dangerous trap.
As shown in the diagram, if the PR author rebases or force-pushes on GitHub, the targeted commit hash vanishes.
Running uv sync --locked in CI or Production fails with "upload-pack: not our ref"!
-->

---

# **Why Direct Git URLs are a Time Bomb 💣**

> 1. 💥 **Upstream Force-Push / Rebase**: Targeted commit disappears ➡️ CI/Prod builds break instantly.
> 2. 🗑️ **Closed PR or Deleted Branch**: Dependency becomes unavailable.
> 3. 🔓 **Lack of Immutability**: Pointing to branch names exposes your project to unvetted changes.
> 4. 🔄 **Local Dev Friction**: Switching from remote Git URL to a local editable clone (`editable = true`) is manual.

### ❓ **How to vault (immutably archive) these VCS references while keeping local dev fast and smooth?**

<!--
Direct Git URLs are a ticking time bomb for four main reasons:
1. If the PR author force-pushes or rebases, the commit hash vanishes and your CI or prod build breaks immediately.
2. If the PR gets closed or the branch is deleted, your build fails completely.
3. Branch references lack immutability.
4. Switching between a remote Git URL and a local editable clone to work on the PR locally is clumsy and manual.

So how can we immutably archive these VCS references while keeping local development fast and frictionless?
-->

---

<!-- _class: lead -->
# **3. DEEP DIVE: `uvault` 🛡️**
### A dedicated VCS workflow tool for `uv`

<!--
This brings us to section three: a deep dive into uvault, a tool designed specifically to solve this workflow problem.
-->

---

# **What is `uvault`?**

- 🛠️ **Standalone CLI** executed via `uvx` (`uvx --with uvault[github] uvault <command>`).
- 💡 Inspired by **`pip-preserve-requirements`** (by Stéphane Bidoul).
- 🎯 **3 Core Pillars**:

1. 🔒 **Vaulting (Immutability)**: Archives PR/branch commits into your organization's Vault repository as immutable tags (`ppr-<sha>`).
2. 💻 **Local Dev Mode**: Switches any dependency into local `editable` mode (`./.src/`) in 1 second to contribute or test.
3. 📊 **Status Monitoring (`uvault status`)**: Alerts on PR state (merged, closed, new remote commits, orphaned commits).

<!--
uvault is a standalone CLI utility run seamlessly with uvx. It was inspired by Stéphane Bidoul's pip-preserve-requirements.

It stands on three core pillars:
1. Immutability via Vaulting: it mirrors and freezes PR commits into your organization's private Vault repository using immutable tags formatted as ppr-<sha>.
2. Instant Local Dev: it switches any dependency into a local editable clone inside ./.src/ in seconds.
3. Status Monitoring: uvault status actively tracks whether upstream PRs get merged, closed, updated, or rebased.
-->

---

# **`uvault` Vaulting Architecture 🛡️**

![bg right:48% contain](images/diagram_vaulting.svg)

### Immutable preservation with `ppr-<sha>` tags

- **`uvault sync`** automatically pushes PR commits to your organization's Vault repository (`my-org-vault`).
- Creates an immutable tag like `ppr-a1b2c3d` (or `pjt-19.0.1.2.3`).
- Updates `pyproject.toml` to point to your secure Vault tag.

> 🔒 **Guarantee**: Even if the upstream PR is rebased or deleted, your Vault retains the commit ➡️ **CI/Prod builds NEVER break!**

<!--
Here is how uvault solves the problem. It mirrors and tags the exact PR commit inside your organization's Vault repository. Even if the author force-pushes or deletes the PR upstream, your Vault retains the exact commit permanently.
-->

---

---

# **`uvault` Global Workflow**

![bg right:48% contain](images/diagram_uvault_workflow.svg)

### End-to-end VCS lifecycle

1. **`uvault add`**: Declare VCS intention.
2. **`uvault sync`**: Archive commit to Vault (`ppr-<sha>` / `tag_prefix`).
3. **`uvault status`**: Monitor upstream PR status.
4. **`uvault develop`**: Switch to local editable clone (`./.src/`).
5. **`uvault release`**: Freeze immutable release tag on deploy.

<!--
Here is the overall workflow of uvault.
First, you declare your dependency intention using uvault add.
Second, uvault sync fetches the target commit and creates an immutable tag in your Vault repository.
From there, you can monitor upstream changes with uvault status or switch into local development mode with uvault develop.
Finally, when preparing a production release, uvault release freezes immutable production tags.
-->

---

# **Declare Intent (`uvault add`)**

Instead of manually editing `pyproject.toml`, declare a VCS dependency **intention**:

```bash
uvx uvault add odoo-addon-partner-firstname \
  https://github.com/OCA/partner-contact \
  --pr 123 \
  --subdirectory setup/partner_firstname
```

### Result in `pyproject.toml`:

```toml
[tool.uvault.sources]
odoo-addon-partner-firstname = { git = "https://github.com/OCA/partner-contact", pr = 123, subdirectory = "setup/partner_firstname" }
```

> ℹ️ *Note: `uvault add` configures the intention in `[tool.uvault.sources]`. It does not touch `[tool.uv.sources]` or lockfile yet.*

<!--
Presenter Note: Step 1 (git checkout step-1-uvault)
Instead of hand-editing pyproject.toml, you run uvault add providing package name, repo URL, PR number, and subdirectory.
uvault records this intention under [tool.uvault.sources].
-->

---

# **Vault & Freeze (`uvault sync`)**

### Run synchronization

```bash
uvx uvault sync
```

### What happens under the hood? ⚙️

1. **Fetches** the exact commit of PR #123 (`refs/pull/123/head`).
2. **Auto-forks** via GitHub API if repo doesn't exist in your Vault org yet.
3. **Pushes immutable tag** (`ppr-<sha>`) to your Vault repo (`my-org-vault/partner-contact`).
4. **Updates `[tool.uv.sources]`** with the secure Vault reference:

```toml
[tool.uv.sources]
odoo-addon-partner-firstname = { git = "https://github.com/my-org-vault/partner-contact", tag = "ppr-a1b2c3d4e5f6...", subdirectory = "setup/partner_firstname" }
```

> 🏷️ *Tag prefix note: `ppr-` comes from `pip-preserve-requirements`. Custom prefixes (e.g., `tag_prefix = "apycod"`) are configurable in `[tool.uvault]`!*

<!--
Presenter Note: Step 2 (git checkout step-2-uvault)
Under the hood, uvault fetches the exact commit of the PR, automatically forks the repo into your Vault organization if it's not there yet, and pushes an immutable tag like ppr-<sha> (or custom prefix like apycod).
-->

---

# **Monitor PR Status (`uvault status`)**

### Stay in control of external dependencies

```bash
uvx uvault status
```

### Diagnostics provided by `uvault status`:

- 🟢 **PR Merged**: The OCA PR was merged upstream! Switch back to standard PyPI release.
- 🔴 **PR Closed**: PR closed without merging.
- ⚡ **New Remote Commits**: New commits pushed to PR (`uvault sync --update` to fetch).
- ⚠️ **Orphaned Commit**: Upstream PR was rebased/force-pushed! Your Vault retains the old commit: **your prod build remains 100% functional**!

<!--
Presenter Note: Step 3 (git checkout step-3-uvault)
This command queries GitHub API to give you clear diagnostics:
- Green light if the PR was merged upstream.
- Red light if closed.
- Lightning indicator if new commits were added to the PR.
- Warning if the PR commit was orphaned by an upstream rebase. Your Vault retains the original commit so production keeps running smoothly!
-->

---

# **Ultra-Fast Local Dev (`uvault develop`)**

Need to modify the OCA PR or add a feature locally?

```bash
uvx uvault develop odoo-addon-partner-firstname my-feature-branch
```

### Automatic actions performed by `uvault develop`:
1. **Clones** repo to `./.src/partner-contact` & configures remotes.
2. **Switches `pyproject.toml`** to local `editable` mode (`path = "./.src/..."`).
3. Run **`uv sync`**: local changes are instantly live in Odoo!

> 🛡️ **Safety Net (`uvault-check`)**: Prevent committing local editables with `uvault`:

```yaml
- repo: https://github.com/petrus-v/uvault
  rev: v0.6.1
  hooks:
    - id: uvault-check   # Reverts editables & restores Vault sources before uv-lock!
```

<!--
Presenter Note: Step 4 (git checkout step-4-uvault)
When you need to fix a bug in the OCA PR or add local customization, run uvault develop <package> <branch>.
uvault automatically clones into ./.src/, configures remotes, and flips pyproject.toml into editable mode.
Use uvault-check in pre-commit (placed before Astral's uv-lock hook) to ensure editable paths are never committed by mistake!
-->

---

# **Release Lifecycle & PEP 440 (`uvault release`)**

![bg right:48% contain](images/diagram_release_lifecycle.svg)

### PEP 440 Versioning Rules
`1.0.1.dev0` (dev) < `1.0.1` (final release) < `1.0.2.dev0`.

### Automation with `bump-my-version`:

```bash
# 1. In dev: pyproject.toml version = 1.0.1.dev0
# 2. Final Production Release tag:
uvx bump-my-version bump release
# -> Pre-commit hook runs `uvault release` & freezes tag v1.0.1!

# 3. Next Dev cycle:
uvx bump-my-version bump patch --no-tag  # -> moves to 1.0.2.dev0
```

<!--
Presenter Note: Step 5 (git checkout step-5-uvault)
uvault integrates with bump-my-version. When you bump to a production release, the pre-commit hook automatically executes uvault release, freezing an immutable production release tag (e.g. 1.0.1+apycod.pkg).
-->

---

# **Comparison Summary**

| Problem | Classic Approach | With `uv` + `hatch-odoo` + **`uvault`** |
| :--- | :--- | :--- |
| **Addons Path Setup** | Complex `--addons-path` in `odoo.conf` | 🪄 Automatic via `site-packages` & `hatch-odoo` |
| **Reproducibility** | Desynchronized `requirements.txt` | 🔒 Unified & deterministic lockfile (`uv.lock`) |
| **Dependencies on OCA PRs** | Direct Git URL (volatile & risky) | 🛡️ Immutable vaulting with `ppr-<sha>` tag |
| **Local Dev Switch** | Manual clone & path edits | 💻 Single command `uvault develop` (`./.src/`) |
| **Missing Commit Disruption**| Broken CI / Prod build without warning | ⚓ Permanently preserved in your Vault repo |

<!--
To summarize the key benefits of this modern stack:
- Addons Path configuration is completely eliminated thanks to hatch-odoo and site-packages.
- Reproducibility is guaranteed via deterministic uv.lock files instead of uncoordinated requirements files.
- OCA PR dependencies are securely vaulted with immutable tags.
- Local dev switching takes a single command.
- And CI/Production builds are completely protected from deleted or rebased upstream commits.
-->

---

# **Resources & Links 🔗**

- 📦 **`uv` by Astral**: [github.com/astral-sh/uv](https://github.com/astral-sh/uv)
- ⚙️ **`hatch-odoo` & `whool`**:
  - [github.com/sbidoul/hatch-odoo](https://github.com/sbidoul/hatch-odoo)
  - [github.com/sbidoul/whool](https://github.com/sbidoul/whool)
- 🛡️ **`uvault`**:
  - GitHub Repo: [github.com/petrus-v/uvault](https://github.com/petrus-v/uvault)
  - Documentation: [uvault.apycod.com](https://uvault.apycod.com)
- 📜 **`pip-preserve-requirements`**: Original inspiration by Stéphane Bidoul.

<!--
Here are key links and resources to explore further:
- The uv repository by Astral.
- hatch-odoo and whool by Stéphane Bidoul.
- The uvault repository at github.com/petrus-v/uvault and documentation at uvault.apycod.com.
- And pip-preserve-requirements, which served as the original inspiration.
-->

---

<!-- _class: lead -->

# **Thank You!**

### Questions & Answers 💬

**OCA Days 2026**

<!--
Thank you very much for your time and attention!
I'm now happy to answer any questions you might have.
-->
