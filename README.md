# Logto Helm Chart

A Helm chart for deploying [Logto](https://logto.io/) Identity Service on Kubernetes with optional PostgreSQL database.

## Prerequisites

- Kubernetes 1.16+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)

## Installation

```bash
# Add the repository
helm repo add logto https://charts.logto.io

# Install the chart
helm install my-logto logto/logto
```

## Configuration

The following table lists the configurable parameters of the Logto chart and their default values.

### Global Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of Logto replicas | `1` |
| `image.repository` | Logto image repository | `svhd/logto` |
| `image.tag` | Logto image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |

### Logto Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `logto.trustProxyHeader` | Trust proxy headers when behind reverse proxy | `true` |
| `logto.endpoint` | Public URL for Logto API | `""` |
| `logto.adminEndpoint` | Public URL for admin console | `""` |

### Service Configuration

The service exposes two ports:
- Main API endpoint (3001)
- Admin endpoint (3002)

```yaml
service:
  type: ClusterIP
  ports:
    - name: http-main
      port: 3001
    - name: http-admin
      port: 3002
```

### PostgreSQL Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `postgresql.enabled` | Enable PostgreSQL deployment | `true` |
| `postgresql.auth.username` | PostgreSQL username | `postgres` |
| `postgresql.auth.password` | PostgreSQL password | `p0stgr3s` |
| `postgresql.auth.database` | PostgreSQL database name | `logto` |
| `postgresql.persistence.enabled` | Enable persistence | `true` |
| `postgresql.persistence.size` | PVC size | `8Gi` |

### Resource Requirements

Default resource limits:

```yaml
resources:
  limits:
    cpu: 1000m
    memory: 1024Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

## Example Installation

1. Create a values file (`values.yaml`):

```yaml
logto:
  endpoint: "https://logto.example.com/api"
  adminEndpoint: "https://logto.example.com/admin"

ingress:
  enabled: true
  hosts:
    - host: logto.example.com
      paths:
        - path: /
          pathType: ImplementationSpecific

postgresql:
  auth:
    password: your-secure-password
```

2. Install the chart:

```bash
helm install my-logto logto/logto -f values.yaml
```

## Health Checks

The Logto application exposes a health check endpoint at `/api/.well-known/health` which is used by Kubernetes probes to determine pod health.

## Notes

- For production deployments, it's recommended to:
  - Use a secure PostgreSQL password
  - Enable ingress with TLS
  - Configure proper resource limits
  - Use a specific image tag instead of `latest`
  - Consider increasing PostgreSQL PVC size based on your data retention needs

## License

This Helm chart is available under the MIT license.
