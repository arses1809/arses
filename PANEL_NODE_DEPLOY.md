# RVG split deployment: Panel + Node

This version supports two explicit roles through `RVG_ROLE`:

- `panel`: management/UI/API only. It does **not** register VLESS/Trojan/Shadowsocks WebSocket relays, XHTTP routes, HTTP proxy, MTProto processes, Railway TCP-proxy helpers, or local link creation.
- `node`: runs the actual protocol transports and exposes the existing `/api/node/*` management API.

## 1. Deploy the panel

Use the repository on Railway with the existing `railway.json`.

Environment:

```text
RVG_ROLE=panel
ADMIN_PASSWORD=<strong-password>
DATA_DIR=/data
```

A persistent Railway volume and/or Redis is recommended for the panel state.

## 2. Deploy a node

Run the same repository on a Linux VPS/provider that permits your intended proxy workload.

Environment:

```text
RVG_ROLE=node
ADMIN_PASSWORD=<strong-password>
DATA_DIR=/data
```

Start command:

```text
python main.py
```

Expose the node's HTTPS endpoint to the panel. If TLS is terminated by a reverse proxy, set the node host to the public hostname, not an internal address.

## 3. Connect the node to the panel

1. Open the node dashboard.
2. In **Nodes / Node Keys**, create a node key.
3. Copy the generated `rvg-...` key.
4. Open the panel dashboard → Nodes → Connect Node.
5. Paste the key and enter the node password if one was configured.
6. The panel performs the existing handshake and stores the node.

The key is intentionally issued by the node: its encoded host tells the panel where to connect.

## 4. Creating links

For a split deployment, create links from the panel by selecting a node. The panel calls the node's `/api/nodes/{node_id}/links` endpoint. The node generates the share URL using its own public host, so client configurations point to the node rather than the panel.

## 5. Subscriptions

The panel's existing node aggregation can include node links in central subscription groups. The returned share links remain node addresses; the panel is not used as the traffic relay.

## 6. Important security settings

- Never use the old default admin password `123456`.
- Put the node behind HTTPS.
- Keep the node key private and revoke/replace it if exposed.
- Prefer a dedicated node for protocol traffic and a separate persistent store for its state.
- This split changes where traffic is handled; it does not bypass a provider's acceptable-use policy. Use a provider that explicitly permits the workload.
