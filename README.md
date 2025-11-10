# ERPNext Image Mirror

This repository automates mirroring of the upstream Docker Hub images (ERPNext + Bitnami dependencies) into a registry that complies with the "no docker.io" policy enforced in your production cluster. The default destination is GitHub Container Registry (GHCR), but you can override it at runtime.

## Secrets / Variables

Create these repository secrets (Settings → Secrets and variables → Actions):

| Secret name        | Purpose                                   |
|--------------------|-------------------------------------------|
| `DOCKERHUB_USERNAME` | Docker Hub username used for pulling the upstream images |
| `DOCKERHUB_TOKEN`    | Docker Hub PAT/password for the above user |

You do **not** need a separate GHCR token—the workflow uses GitHub’s built‑in `GITHUB_TOKEN` with `packages: write` permission to push directly to `ghcr.io/<org>/<repo>`.

## Usage

1. Go to **Actions → Mirror ERPNext Images**.
2. Click **Run workflow** and provide:
   - Target registry path (default: `ghcr.io/fedoratechnologies/erpnext`)
   - Tags for ERPNext, MariaDB, and Redis (defaults provided)
3. The workflow installs `skopeo`, logs into Docker Hub using the secrets above, logs into GHCR using `GITHUB_TOKEN`, and copies:
   - `frappe/erpnext:<tag>` → `<target>/erpnext:<tag>`
   - `bitnami/mariadb:<tag>` → `<target>/mariadb:<tag>`
   - `bitnami/redis:<tag>` → `<target>/redis:<tag>`

After the run finishes, update your GitOps repo (`fedoratechnologies/erpnext`) to reference the mirrored image paths in `environments/production/erpnext-values.yaml`, then let Argo CD sync the deployment.

## Preservation of SuiteCRM Script

Per your requirement, the old SuiteCRM deployment helper remains only in git (in the GitOps repo). This repository focuses solely on ERPNext image mirroring.
