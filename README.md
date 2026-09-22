# OCA Days 2026: Odoo dev environement

This is the example branch, the presentation is on the main branch.

## Running the project

```bash
uv run odoo  \
  -d ocadays26 -i ocadays26 \
  --stop-after-init \
  --no-http \
  --without-demo=True
```


## Running uvault

Get VCS dependencies status:

```bash
uvx uvault[github] status --format inline
```
