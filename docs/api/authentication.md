# Authentication

Kite supports programmatic access through API keys. An API key authenticates as a special user and follows the same RBAC model as interactive users.

## Login attempt blocking

Credential-based logins are temporarily blocked by client IP after repeated invalid password or MFA attempts. Kite allows up to 10 failures within 1 minute; the next failure blocks further credential login attempts from that client IP for 5 minutes.

When Kite runs behind an ingress or load balancer, the client IP used by this limiter depends on `TRUSTED_PROXIES`. See [Environment Variables](/config/env#environment-variables) for how to configure trusted proxy ranges.

## API key format

The full API key format is:

```text
kite<ID>-<SECRET>
```

Use the full value directly in the `Authorization` header. Do not prepend `Bearer`.

```http
Authorization: kite12-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## Where to configure it

Users with the `admin` role can create API keys in **Settings -> API Keys**.

After creating a key, copy the full value and use it as the `Authorization` header for API requests.

## Permissions

API keys use the same RBAC model as regular users.

- Creating an API key does not automatically grant any resource permissions.
- Resource access under `/api/v1/...` is still checked by RBAC.
- Admin APIs under `/api/v1/admin/...` require the caller to have the `admin` role.
- Cluster resource APIs usually also require `x-cluster-name`.

## Authenticate requests

Example:

```bash
curl \
  -H "Authorization: kite12-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  -H "x-cluster-name: demo-cluster" \
  https://kite.example.com/api/v1/pods/default
```

Notes:

- Resource endpoints under `/api/v1/...` usually also require `x-cluster-name`.
