# RVG Gateway — Panel / Node deployment

This build supports two runtime roles from the same codebase:

- `RVG_ROLE=panel`: management UI/API only. It does not import or register proxy relay modules and does not start MTProto/Zeus processes.
- `RVG_ROLE=node`: proxy worker. It loads the protocol modules and exposes the Node API used by the central panel.

## Panel

Set:

```env
RVG_ROLE=panel
ADMIN_PASSWORD=change-this-to-a-long-random-password
DATA_DIR=/data
# Optional:
# REDIS_URL=redis://...
```

The panel's `railway.json` start command is `python main.py`.

## Node

Set:

```env
RVG_ROLE=node
ADMIN_PASSWORD=change-this-to-a-long-random-password
PUBLIC_DOMAIN=node.example.com
DATA_DIR=/data
# Optional:
# REDIS_URL=redis://...
```

Use a provider/network that permits the proxy workload. Do not route proxy traffic through a platform that prohibits it.

## Connecting a Node to the Panel

1. Run the node with `RVG_ROLE=node` and HTTPS.
2. In the panel, create a Node key and connect the node using its public hostname.
3. The panel communicates with the node through HTTPS and `X-RVG-Node-Key`.
4. Proxy endpoints remain on the node; the panel only manages them.

## Important

The project ZIP must contain `main.py`, `protocol/`, `pages.py`, etc. at the archive root. Do not deploy a ZIP that contains an extra top-level `RVG-main/` directory unless the deployment system is configured to use that directory as the application root.
