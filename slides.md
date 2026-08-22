---
marp: true
theme: gaia
_class: lead
paginate: true
backgroundColor: #ffffff
backgroundImage: url('https://marp.app/assets/hero-background.svg')
style: |
  section {
    font-size: 78%;
    padding: 35px 50px;
    color: #2b3a4a;
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
    font-size: 72%;
    background-color: #282c34;
    color: #abb2bf;
    border-radius: 6px;
    padding: 12px 16px;
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
    font-size: 90%;
    color: #2b3a4a;
  }
  table {
    font-size: 75%;
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

# **About & Agenda**

- **About**: Odoo Developer, OCA Contributor, creator of `uvault`
- **Context**: Historically, Odoo struggled with standard Python tooling (e.g., `addons_path`). Today, it integrates seamlessly (`uv`, `pyproject.toml`)!

### Agenda today:
1. 💡 **Quick Demo**: Setup an Odoo project with `uv` & `hatch-odoo`
2. ⚙️ **Under the Hood**: How Odoo became a standard Python project (`whool`, PyPI)
3. 🔒 **Reproducibility**: Freeze your environment with `uv.lock`
4. 💣 **The Problem**: Managing unreleased dependencies (open OCA PRs)
5. 🛡️ **DEEP DIVE `uvault`**: Vaulting, local editable dev & release lifecycle

<!--
To introduce myself briefly: I'm an Odoo developer, long-time OCA contributor, and the creator of the uvault CLI.
Historically, Odoo struggled to integrate smoothly with standard Python tooling due to legacy mechanisms—most notably namespace management and the infamous `addons_path`. Today, thanks to tools like uv, pyproject.toml, whool, and hatch-odoo, Odoo bridges this gap and fits naturally into the modern Python ecosystem!

Here is our agenda for today:
1. A quick demo initializing a Pythonic Odoo project.
2. A look under the hood at how OCA modules and whool leverage PyPI and namespaces.
3. How we achieve absolute reproducibility using uv.lock.
4. The classic friction of unmerged Git dependencies.
5. And a deep dive into uvault for vaulting PRs, local editable dev, and managing release lifecycles.
-->

---

<!-- _class: lead -->
# **1. Demo: An Odoo project managed like any Python project**

<!--
Let's dive right into section one: the live demo.
Let's see how an Odoo project can be initialized and managed just like any standard Python project today.
-->

---

# **1. Demo: Initialization & Structure**

<!-- TODO: faire la démo comme suit: -->
* Step 0: initialize project with uv init
* Step 1: Install Odoo with a custom module
* Step 2: Add OCA dependencies
* Step 3: Use hatch-odoo to manage addons-path
* Step 4: Use hatch-odoo dynamic dependencies
* Step 5: use module (mis_builder) from an OCA's PR
* Step 6: use editable mode to contribute to OCA
* Step 7: back to PR reference
* Explain garbadge collection on git refs

  
### Initialize the project with `uv`

```bash
uv init --bare my-odoo-project
cd my-odoo-project
```

### Modern repository layout

```text
my-odoo-project/
├── pyproject.toml         # Python/Odoo metadata & dependencies
├── uv.lock                # Deterministic locked dependency graph
└── odoo/addons/
    └── ocadays_2026/      # Your custom Odoo module
```

<!--
Everything starts with a single command: `uv init --bare`. In milliseconds, your project root is initialized.
Looking at the repository structure, notice how clean it is. We have pyproject.toml at the root for configuration and dependencies, uv.lock guaranteeing deterministic builds, and a standard folder for custom Odoo modules.
-->

---

# **1. Demo: `pyproject.toml` Configuration**

```toml
[build-system]
requires = ["hatchling", "hatch-odoo"]
build-backend = "hatchling.build"

[project]
name = "my-odoo-project"
version = "17.0.1.0.0.dev0"
dependencies = [
    "odoo>=17.0,<17.1",
    "odoo-addon-partner-firstname>=17.0.0.0.0",
    # Freeze Odoo pinned dependencies according your python version
]
```

### Immediate execution 🚀

```bash
# Run Odoo with auto-synced virtual environment!
uv run odoo
```

<!--
Here is what pyproject.toml looks like.
We specify hatchling as our build backend with the hatch-odoo plugin. Under dependencies, we declare Odoo core alongside any OCA modules using standard Python package names.
To start Odoo? You don't even need to remember to activate a virtualenv. Just type `uv run odoo`, and uv will create, sync, and execute inside an up-to-date virtual environment automatically!
-->

---

<!-- _class: lead -->
# **2. Under the Hood: Modern Python Paradigm applied to Odoo**

<!--
Now let's move to section two and look under the hood to see how this seamless integration actually works behind the scenes.
-->

---

# **2. What is `uv`?**

- 🦀 **Written in Rust** by Astral (creators of Ruff).
- ⚡ **10x to 100x faster** than `pip`, `pip-tools`, or `poetry`.
- 🧰 **All-in-one tool**: replaces `pip`, `virtualenv`, `pip-tools`, `pyenv`, `pipx`, `poetry`.

> ### The `uv run` paradigm
> - Manages and synchronizes `.venv` **automatically**.
> - Executes commands in an up-to-date virtual environment.
> - No more forgotten `source .venv/bin/activate` or missed `pip install` after `git pull`!

<!--
If you haven't tried uv yet, it's the game-changing Python package manager built in Rust by Astral, the creators of Ruff.
It is 10 to 100 times faster than traditional tools and replaces pip, virtualenv, poetry, and pyenv all in one binary.
The core paradigm shift is `uv run`: it manages and syncs your .venv transparently in the background. No more broken builds because someone forgot to activate their virtualenv or run pip install after pulling latest changes.
-->

---

# **2. The OCA Miracle: PyPI & `whool`**

### Did you know EVERY OCA module is published on PyPI? 📦

- A huge thanks to **Stéphane Bidoul** for the **`whool`** build backend!
- OCA repositories build standard Python wheels on **pypi.org**.
- Naming convention on PyPI: `odoo-addon-<module>`
  - E.g.: `account_financial_report` ➡️ `odoo-addon-account-financial-report`

You can add dependencies like this:

`uv add odoo-addon-account-financial-report`

<!--
A major milestone for the Odoo ecosystem is PyPI distribution for OCA modules.
Huge credit goes to Stéphane Bidoul for creating `whool`. Thanks to this build backend, every OCA module publishes standard Python wheels on pypi.org!
The naming convention is straightforward: `odoo-addon-<module_name>`.
Adding any OCA module to your project is as simple as running `uv add odoo-addon-account-financial-report`.
-->

---

# **2. How `hatch-odoo` Solves Addons Path**

### No more endless `--addons-path=...` in `odoo.conf`! 🎉

1. **Standard Python Installation**: Modules are installed in `site-packages/odoo/addons/` via Python namespace packages or entry points.
2. **`hatch-odoo` handles everything**:
   - Inspects `__manifest__.py` and the `depends` key.
   - Dynamically reconstructs the `odoo.addons` namespace.
   - Automatically resolves dependency paths.

> **Result**: Modules are naturally imported into Odoo without manual addons-path configuration!

<!--
You might wonder: how does Odoo know where to find these addons without a long `--addons-path` parameter in `odoo.conf`?
That's where `hatch-odoo` comes in. When packages are installed, modules live inside `site-packages/odoo/addons/` as Python namespace packages.
`hatch-odoo` inspects the manifest files and dependencies to dynamically reconstruct the odoo.addons namespace. Odoo can then import installed addons naturally with zero manual addons-path configuration.
-->

---

# **2. Adding Modules: Dynamic Dependencies**

### Declare dynamic dependencies in `pyproject

```toml
[project]
...
dynamic = [
    "dependencies",
]

# Enable the hatch-odoo metadata hook to generate dependencies from addons manifests.
[tool.hatch.metadata.hooks.odoo-addons-dependencies]

[tool.hatch-odoo]
# If our addons have non standard version numbers, let's help hatch-odoo discover the Odoo version.
odoo_version_override = "19.0"
dependencies = [
    "click-odoo-contrib",
    "Pillow==11.1.0 ; python_version >= '3.13'",  # (Noble) Mostly to have a wheel package
    ...
]
```


### Simply declare dependencies in `__manifest__.py`! 💡

```python
# odoo/addons/ocadays_2026/__manifest__.py
{
    "name": "OCA Days 2026 Custom Module",
    "version": "17.0.1.0.0",
    "depends": [
        "base",
        "partner_firstname",  # ➡️ odoo-addon-partner-firstname
    ],
}
```

### How `hatch-odoo` resolves it automatically:
- `hatch-odoo` reads `depends` in your custom module manifests.
- Dynamically converts Odoo module names into PyPI package requirements (`odoo-addon-<module>`).
- Running `uv sync` or `uv run odoo` automatically fetches & locks them!

<!--
Thanks to hatch-odoo's dynamic dependency resolution, adding an OCA module to your project is completely seamless.
You don't even need to manually edit pyproject.toml for every OCA dependency!
Instead, you simply declare "partner_firstname" in the "depends" key of your custom module's manifest file.
hatch-odoo automatically maps this entry to the corresponding PyPI wheel name—odoo-addon-partner-firstname—and uv fetches, locks, and installs it instantly.
-->



> ⚠️ Because of the dynamic dependencies, you won't be able to `uv add`


---

<!-- _class: lead -->
# **3. Reproducibility: `uv.lock` from Dev to Prod**

<!--
Section three focuses on reproducibility. How do we guarantee that what runs on a developer's laptop behaves identically in CI, staging, and production?
-->

---

# **3. Absolute Reproducibility: `uv.lock`**

### The role of `uv.lock`

- **Cross-platform lockfile** (multi-OS, multi-Python versions).
- Stores the **exact** version and SHA256 hash of every dependency (core Odoo, OCA modules, C/Python libs).

### Daily key commands:

```bash
uv lock                  # Generate / update lockfile
uv sync                  # Sync virtualenv with lockfile
uv tree                  # Visualize complete dependency graph
uv lock --upgrade-package odoo-addon-partner-firstname # Update a specific module
```

> 🎯 **Guarantee**: Local Dev == CI == Staging == Production.

<!--
The secret weapon here is `uv.lock`. It's a cross-platform, deterministic lockfile.
It locks exact versions and SHA256 checksums for every single dependency: core Odoo, OCA modules, and underlying C/Python libraries.
Commands like `uv lock`, `uv sync`, and `uv tree` make dependency management clean and predictable.
The ultimate guarantee: your local dev environment matches CI, Staging, and Production down to the exact byte.
-->

### Démo VCS Dependencies

We have seen how to
* Add VCS dependencies
* Use VCS dependency in develop/editable mode
* Add site-packages folder in your IDE (to quickly search/read/add breakpoint in other modules)

Avoid to commit pyproject with develop/editable mode it won't work on you teamate computer !


### Commits can be garbadged

```bash
$ uv sync --locked
Resolved 74 packages in 0.84ms
   Updating https://github.com/OCA/mis-builder.git (refs/pull/827/head)                                                                                                                                                            × Failed to download and build `odoo-addon-mis-builder @ git+https://github.com/OCA/mis-builder.git@88d87101821126a62aa3887d7c34c64cc067d95a#subdirectory=mis_builder`
  ├─▶ Git operation failed
  ├─▶ failed to fetch into: /home/pverkest/.cache/uv/git-v0/db/de85351658deeeac
  ├─▶ failed to fetch commit `88d87101821126a62aa3887d7c34c64cc067d95a`
  ╰─▶ process didn't exit successfully: `/home/pverkest/.local/bin/git fetch --force --update-head-ok 'https://github.com/OCA/mis-builder.git'
      '+88d87101821126a62aa3887d7c34c64cc067d95a:refs/commit/88d87101821126a62aa3887d7c34c64cc067d95a'` (exit status: 128)
      --- stderr
      fatal : erreur distante : upload-pack: not our ref 88d87101821126a62aa3887d7c34c64cc067d95a


hint: `odoo-addon-mis-builder` was included because `ocadays-2026-odoo-dev` (v0.1.0) depends on `odoo-addon-mis-builder`
```

---

<!-- _class: lead -->
# **4. The Real Problem: Unreleased Dependencies & Volatile PRs**

<!--
This setup sounds ideal so far, right? But now let's address the real-world headache that every Odoo team faces.
-->

---

# **4. Everyday Life of Odoo / OCA Developers 😅**

You work on a project. You need a feature or bugfix available only on:
- An **open OCA Pull Request** (`OCA/partner-contact#123`), not merged yet.
- A temporary working branch on a fork.

```toml
# Naive approach using Git in pyproject.toml / uv:
[tool.uv.sources]
odoo-addon-partner-firstname = { git = "https://github.com/OCA/partner-contact", rev = "refs/pull/123/head", subdirectory = "partner_firstname" }
```

<!--
In daily Odoo project development, we constantly need features or bugfixes that exist only on an open OCA Pull Request that hasn't been merged yet, or on a temporary fork branch.
The naive reaction is to add a direct Git URL in pyproject.toml pointing directly to the remote PR ref or branch. Let's see why this creates a major risk.
-->

---

# **4. Why Direct Git URLs are a Time Bomb 💣**

> 1. 💥 **Upstream Force-Push / Rebase**: Targeted commit disappears ➡️ CI/Prod builds break instantly.
> 2. 🗑️ **Closed PR or Deleted Branch**: Dependency becomes unavailable.
> 3. 🔓 **Lack of Immutability**: Pointing to branch names exposes your project to unvetted changes.
> 4. 🔄 **Local Dev Friction**: Switching from remote Git URL to a local editable clone (`editable = true`) to modify the PR is tedious.

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
# **5. DEEP DIVE: `uvault` 🛡️**
### A dedicated VCS workflow tool for `uv`

<!--
This brings us to section five: a deep dive into `uvault`, a tool designed specifically to solve this workflow problem.
-->

---

# **5. What is `uvault`?**

- 🛠️ **Standalone CLI** executed via `uvx` (`uvx --with uvault[github] uvault <command>`).
- 💡 Inspired by **`pip-preserve-requirements`** (by Stéphane Bidoul).
- 🎯 **3 Core Pillars**:

1. 🔒 **Vaulting (Immutability)**: Archives PR/branch commits into your organization's Vault repository as immutable tags (`ppr-<sha>`).
2. 💻 **Local Dev Mode**: Switches any dependency into local `editable` mode (`./.src/`) in 1 second to contribute or test.
3. 📊 **Status Monitoring (`uvault status`)**: Alerts on PR state (merged, closed, new remote commits, orphaned commits).

<!--
`uvault` is a standalone CLI utility run seamlessly with `uvx`. It was inspired by Stéphane Bidoul's `pip-preserve-requirements`.

It stands on three core pillars:
1. Immutability via Vaulting: it mirrors and freezes PR commits into your organization's private Vault repository using immutable tags formatted as `ppr-<sha>`.
2. Instant Local Dev: it switches any dependency into a local editable clone inside `./.src/` in seconds.
3. Status Monitoring: `uvault status` actively tracks whether upstream PRs get merged, closed, updated, or rebased.
-->

---

# **5. `uvault`: Global Workflow**

```text
                  +-----------------------------------------+
                  | Declare intent: uvault add              |
                  +-----------------------------------------+
                                       |
                                       v
                  +-----------------------------------------+
                  | Synchronize & Vault: uvault sync        |
                  | (Push immutable tag ppr-<sha> to Vault) |
                  +-----------------------------------------+
                                       |
                     +-----------------+-----------------+
                     |                                   |
                     v                                   v
     +------------------------------+   +------------------------------+
     | Monitor: uvault status       |   | Local Dev: uvault develop    |
     | (PR merged? Force-pushed?)   |   | (Clone ./.src + editable)    |
     +------------------------------+   +------------------------------+
                                                         |
                                                         v
                                        +------------------------------+
                                        | Release: uvault release      |
                                        | (Final release tag 1.0.0)    |
                                        +------------------------------+
```

<!--
Here is the overall workflow of uvault.
First, you declare your dependency intention using `uvault add`.
Second, `uvault sync` fetches the target commit and creates an immutable tag in your Vault repository.
From there, you can monitor upstream changes with `uvault status` or switch into local development mode with `uvault develop`.
Finally, when preparing a production release, `uvault release` freezes immutable production tags.
-->

---

# **5. Step 1: Declare Intent (`uvault add`)**

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
Step one is declaring intent.
Instead of hand-editing pyproject.toml, you run `uvault add` providing the package name, repo URL, PR number, and subdirectory.
uvault records this intention under `[tool.uvault.sources]`. At this stage, it doesn't modify `[tool.uv.sources]` or the lockfile yet—it simply registers what you want to achieve.
-->

---

# **5. Step 2: Vault & Freeze (`uvault sync`)**

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

5. **Run `uv lock`** to update `uv.lock`.

<!--
Step two is vaulting and freezing via `uvault sync`.
Under the hood, uvault fetches the exact commit of the PR, automatically forks the repo into your Vault organization if it's not there yet, and pushes an immutable tag like `ppr-<sha>`.
It then updates `[tool.uv.sources]` to point to your secure Vault tag, and updates `uv.lock`.
Even if the upstream PR is rebased or deleted tomorrow, your Vault guarantees your build will never break.
-->

---

# **5. Step 3: Monitor PR Status (`uvault status`)**

### Stay in control of external dependencies

```bash
uvx uvault status
```

### Diagnostics provided by `uvault status`:

- 🟢 **PR Merged**: The OCA PR was merged upstream! You can plan switching back to standard PyPI release.
- 🔴 **PR Closed**: PR closed without merging.
- ⚡ **New Remote Commits**: New commits pushed to PR (`uvault sync --update` to fetch).
- ⚠️ **Orphaned Commit**: Upstream PR was rebased/force-pushed! Your Vault keeps the old commit: **your prod build remains 100% functional**!

<!--
Step three: monitoring PR status with `uvault status`.
This command queries GitHub API to give you clear diagnostics:
- Green light if the PR was merged upstream, so you can transition back to official PyPI releases.
- Red light if closed.
- Lightning indicator if new commits were added to the PR.
- Warning if the PR commit was orphaned by an upstream rebase. Even in that case, your Vault retains the original commit so production keeps running smoothly!
-->

---

# **5. Step 4: Ultra-Fast Local Dev (`uvault develop`)**

Need to modify the OCA PR or add a feature locally?

```bash
uvx uvault develop odoo-addon-partner-firstname my-feature-branch
```

### Automatic actions performed by `uvault develop`:
1. **Clones** repo to `./.src/partner-contact`.
2. **Configures Git Remotes** (upstream OCA, Vault org fork, `my-feature-branch`).
3. **Switches `pyproject.toml`** to local `editable` mode:

```toml
[tool.uv.sources]
odoo-addon-partner-firstname = { path = "./.src/partner-contact/setup/partner_firstname", editable = true }
```

4. Run **`uv sync`**: local changes are instantly picked up by Odoo!

<!--
Step four: ultra-fast local development.
When you need to fix a bug in the OCA PR or add local customization, run `uvault develop <package> <branch>`.
uvault automatically clones the repository into `./.src/`, configures all Git remotes (upstream, Vault, and your local branch), and flips pyproject.toml into editable mode.
Run `uv sync`, and your local code edits take effect in Odoo instantly!
-->

---

# **5. Step 5: Release Lifecycle & PEP 440 (`uvault release`)**

### PEP 440 Versioning Rules
In Python: `1.0.1.dev0` (pre-release) < `1.0.1` (final release) < `1.0.2.dev0`.
`uvault` automatically handles dev versions (`.dev0`) vs final production releases.

```toml
[tool.uvault]
tag_prefix = "apycod"
release_tag_template = "{project_version}+{tag_prefix_normalized}.{pkg_normalized}"
```

### Automation with `bump-my-version`:

```bash
# 1. In dev: version in pyproject.toml = 1.0.1.dev0
# 2. Final Production Release tag:
uvx bump-my-version bump release
# -> Automatic hook: `uvault release` freezes final release immutable tag (v1.0.1)!

# 3. Prepare next Dev cycle:
uvx bump-my-version bump patch --no-tag  # -> moves to 1.0.2.dev0
```

<!--
Step five: managing the release lifecycle according to PEP 440 rules.
Python uses standard version ordering where `.dev0` indicates pre-release development.
`uvault` integrates with version bumping tools like `bump-my-version`. When you bump to a production release, `uvault release` automatically freezes an immutable production tag (e.g., `v1.0.1`), ensuring end-to-end traceability for production deployments.
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
