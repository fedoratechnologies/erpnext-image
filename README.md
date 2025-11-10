# ERPNext Image Mirror

This repository automates mirroring of the upstream Docker Hub images (ERPNext + Bitnami dependencies) into a registry that complies with the "no docker.io" policy enforced in your production cluster. The mirror runs via GitHub Actions and copies the images to your GitLab Container Registry (or any registry you specify when running the workflow).

## Secrets / Variables

Create the following repository secrets (Settings → Secrets and variables → Actions):

| Secret name        | Purpose                                   |
|--------------------|-------------------------------------------|
| `DOCKERHUB_USERNAME` | Docker Hub username used for pulling the upstream images |
| `DOCKERHUB_TOKEN`    | Docker Hub PAT/password for the above user |
| `GITLAB_USERNAME`    | GitLab username with access to your registry |
| `GITLAB_TOKEN`       | GitLab PAT (scopes: `read_registry`, `write_registry`) |

The workflow accepts a `targetRegistry` input, so you do not need to hardcode a registry path in the repo. For convenience, you can also create a repository variable named `DEFAULT_TARGET_REGISTRY` (e.g., `registry.gitlab.com/fedoratechnologies/erpnext`) and copy/paste it when dispatching the workflow.

## Usage

1. Go to **Actions → Mirror ERPNext Images**.
2. Click **Run workflow** and provide:
   - Target registry path (`registry.gitlab.com/fedoratechnologies/erpnext` by default)
   - Tags for ERPNext, MariaDB, and Redis (defaults provided)
3. The workflow installs skopeo, logs into Docker Hub and GitLab, and copies:
   - `frappe/erpnext:<tag>` → `<target>/erpnext:<tag>`
   - `bitnami/mariadb:<tag>` → `<target>/mariadb:<tag>`
   - `bitnami/redis:<tag>` → `<target>/redis:<tag>`

When the run finishes, update your GitOps repo (`fedoratechnologies/erpnext`) to point at the mirrored registry paths in `environments/production/erpnext-values.yaml`.

## Preservation of SuiteCRM Script

Per your requirement, SuiteCRM deployment snippets remain only in git (in your GitOps repo). This repository holds only automation needed for ERPNext image mirroring.
