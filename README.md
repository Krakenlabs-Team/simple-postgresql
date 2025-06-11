
# simple-postgresql

A simple, configurable Helm chart to deploy **PostgreSQL** as a `StatefulSet`, with optional **ExternalSecret** integration.

---

## Features

- Deploys PostgreSQL as a StatefulSet
- Generates Persistent Volume Claims (PVCs) per instance
- Configurable resources, storage, database/user
- Optional integration with ExternalSecret (eg. SecretManager)
- Safe default configuration
- Lightweight and suitable for local or production use

---

## Requirements

- Kubernetes 1.21+
- Helm 3.x
- (Optional) ExternalSecrets Operator installed if you want to use the ExternalSecret integration.

---

## Installation

```bash
helm repo add krakenlabs https://charts.krakenlabs.com.ar
helm repo update
helm install my-postgres krakenlabs/simple-postgresql
```

Or from local checkout:

```bash
git clone https://github.com/Krakenlabs-Team/simple-postgresql.git
cd simple-postgresql
helm install my-postgres . -f values.yaml
```

---

## Configuration

The following values are configurable in `values.yaml`:

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `replicaCount` | int | `1` | Number of PostgreSQL replicas |
| `image.repository` | string | `postgres` | PostgreSQL container image |
| `image.tag` | string | `15` | Image tag/version |
| `postgres.database` | string | `mydb` | Database name to create |
| `postgres.user` | string | `postgres` | Database user |
| `postgres.port` | int | `5432` | PostgreSQL port |
| `persistence.enabled` | bool | `true` | Enable persistence |
| `persistence.accessModes` | list | `[ReadWriteOnce]` | PVC access mode |
| `persistence.size` | string | `10Gi` | PVC size |
| `persistence.storageClassName` | string | `""` | Storage class name (if empty, uses default) |
| `resources` | object | `{}` | CPU/Memory resources (limits/requests) |
| `externalSecret.enabled` | bool | `true` | Enable ExternalSecret generation |
| `externalSecret.secretStoreKind` | string | `ClusterSecretStore` | External Secret store kind |
| `externalSecret.secretStoreName` | string | `cluster-secret-store` | Secret store name |
| `externalSecret.refreshInterval` | string | `1h` | Refresh interval |
| `externalSecret.remoteSecretName` | string | `postgres-password` | Remote secret name to pull |
| `externalSecret.remoteSecretProperty` | string | `password` | Remote secret property/key to use |

---

## Example Usage with ExternalSecret

If `externalSecret.enabled = true`, the chart will create an `ExternalSecret` that pulls the `POSTGRES_PASSWORD` from the configured remote SecretStore.

Example:

```yaml
externalSecret:
  enabled: true
  secretStoreKind: ClusterSecretStore
  secretStoreName: cluster-secret-store
  refreshInterval: 1h
  remoteSecretName: qa-postgres-password
  remoteSecretProperty: password
```

If you disable it:

```yaml
externalSecret:
  enabled: false
```

You must manually create the Kubernetes Secret:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-postgres-secret
type: Opaque
stringData:
  POSTGRES_PASSWORD: mysupersecretpassword
```

---

## Notes

- The StatefulSet will create `N` PVCs if you set `replicaCount > 1`.
- The Service is `ClusterIP` by default; you can edit it for headless if needed.
- This is a simple chart, not aiming to replace full Postgres operators (like Zalando or Crunchy), but great for lightweight or isolated environments.

---

## Roadmap

- Support init scripts
- Support custom config files
- Add ServiceAccount support (optional)
- Add TLS support (optional)

---

## Maintainers

- KrakenLabs Team

---

## License

MIT

---

## Disclaimer

Use this chart at your own risk. Always test in your environment.