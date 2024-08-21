# Audiobookshelf Helm Chart

This Helm chart deploys the [Audiobookshelf](https://audiobookshelf.org) server on a Kubernetes cluster. 

The default config creates persistent volume claims for /config and /metadata with the most important being /config must not be mounted over NFS due to sqlite3 databases often becoming corrupt over NFS.

## Installation

To install Audiobookshelf using this Helm chart, follow these steps:

### Configuration

Create a `custom.yaml` file to override the default configuration settings. Below is an example configuration that integrates Audiobookshelf with Traefik for ingress, and configures readonly NFS shares where the media files are stored.

```yaml
ingress:
  enabled: true
  annotations:
    kubernetes.io/ingress.class: traefik
  hosts:
    - host: audiobooks.my-domain.com
      paths:
        - path: /
          pathType: ImplementationSpecific

nfs:
  - server: 192.168.0.30
    storage: 1Ti
    name: my-nfs-server
    share:
      - name: audiobooks
        path: /volume1/audiobooks
        mountPath: /audiobooks
```

If required the version of the Audiobookshelf image can be overridden by setting the `image.tag` value in the `custom.yaml` file.

```yaml
image:
  repository: ghcr.io/audiobookshelf/audiobookshelf
  tag: 2.12.3
  pullPolicy: IfNotPresent
```

#### Add the repo and deploy with your customizations

```bash
helm repo add audiobookshelf https://TODOPAGEHERE
helm install audiobookshelf -f custom.yaml audiobookshelf/audiobookshelf
```

## Upgrades

To upgrade to the latest version of helm chart or to modify your settings in custom.yaml after deployment you can run the following to push the changes.

```bash
helm upgrade audiobookshelf -f custom.yaml audiobookshelf/audiobookshelf
```

## Additional Information

For more detailed configuration options and advanced setups, refer to the chart's `values.yaml` file and the Audiobookshelf documentation.
