# GitHub upload — RVG Panel + Node

## IMPORTANT
Upload the **contents of this folder** to the repository root.
Do NOT upload this ZIP as the deployed source.

After upload, the repository root must contain at least:

- `main.py`
- `requirements.txt`
- `railway.json`
- `protocol/`
- `pages.py`
- `central.py`

The `protocol/` directory must be preserved with all of its subdirectories and `__init__.py` files.

## Railway
Set:

`RVG_ROLE=panel`

and a strong `ADMIN_PASSWORD`.

## Node
Set:

`RVG_ROLE=node`

on a provider that permits the intended proxy workload.
