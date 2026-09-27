# ARC runner credentials

Create a GitHub App owned by `SiluniLabs` and grant it these permissions:

- Organization: Self-hosted runners, read and write
- Organization: Metadata, read-only

Install the app in the organization, then edit `secret.sops.yaml` with SOPS:

```sh
sops apps/arc-runners/secret.sops.yaml
```

Replace the three `REPLACE_ME` values with the App ID, installation ID, and
contents of the generated private key. Save the file encrypted; do not commit
the decrypted secret. Do not let Argo CD sync this template before replacing
the placeholders with valid credentials.

ARC's runner scale set is named `cluster-synced`, so workflows must request it
with `runs-on: cluster-synced`. It keeps one idle runner available and scales
up to two concurrent runners. Docker builds use ARC's Docker-in-Docker mode,
which requires privileged containers on the Kubernetes nodes. The scale set
uses the organization's default runner group; restrict that group's repository
access in GitHub before assigning jobs to it.