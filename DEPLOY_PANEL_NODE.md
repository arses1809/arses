# RVG Panel / Node deployment

This version supports two deployment roles through `RVG_ROLE`:

- `panel`: management/control plane. It serves the dashboard/API and manages remote Nodes. It does **not** register VLESS/Trojan/Shadowsocks WebSocket/XHTTP relay routes and does not start local MTProto processes.
- `node`: proxy/data plane. It runs the proxy transports and accepts the existing Node API from a trusted Panel.

## 1) Panel on Railway

Set Railway variables:

```text
RVG_ROLE=panel
DATA_DIR=/data
REDIS_URL=<optional>
```

Keep the existing start command:

```text
python main.py
```

For persistent state, use Redis or a persistent volume for `/data`.

## 2) Node on a compatible VPS/provider

Set:

```text
RVG_ROLE=node
DATA_DIR=/data
```

Start with:

```text
python main.py
```

Expose the Node's HTTPS API and the proxy ports required by the protocols you actually enable. Do not expose the Node management API without authentication/TLS.

## 3) Connect Panel to Node

1. Log in to the Node's dashboard.
2. Open the Node-key management section and create a Node key. Give it only the permissions required by the Panel.
3. Copy the generated Node key.
4. In the Panel's Node management section, use **Connect Node** and paste the key.
5. Select the Node when creating/managing links.

The existing Node API uses `X-RVG-Node-Key` for authentication. The Panel stores the remote Node's host/key metadata and calls the Node over HTTP(S).

## 4) Important behavior changes

- `/api/links` is read-only/empty on `panel` and local link create/update/delete are rejected.
- VLESS/Trojan/Shadowsocks WebSocket routes and VLESS/Trojan XHTTP routes are registered only on `node`.
- MTProto startup/restart is performed only on `node`.
- Railway TCP-proxy/Zeus proxy management endpoints are Node-only.
- The Panel can still aggregate Node links and statistics and can build subscription groups that reference Node links.

## 5) Recommended security

- Use HTTPS between Panel and Node.
- Give each Node its own key and revoke it if compromised.
- Do not put a Node key in frontend JavaScript.
- Set a strong panel password.
- Restrict the Node API with a firewall/reverse proxy where practical.
