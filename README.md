# Microsoft Fabric Deployment Pipeline CI/CD (Service Principal Automation)

Automated provisioning and multi-item release promotion across **Development (`dev`)**, **Test (`test`)**, and **Production (`prod`)** workspaces using a Service Principal (`sp-fabric-ci-cd`) and an Entra AD Security Group (`sg-fabric-data-engineers`) without granting users Workspace Admin rights.

## Included Pipelines & Artifacts

1. **GitHub Actions Workflows (`.github/workflows/`)**:
   - `provision-fabric-pipeline.yml`: Idempotently provisions `fabric-release`, assigns `Admin` role to `sg-fabric-data-engineers`, and maps `dev`, `test`, and `prod` workspaces.
   - `promote-fabric-items.yml`: Accepts comma-separated Dev `item_ids`, auto-detects each item's `itemType`, batch-deploys Dev $\rightarrow$ Test, pauses on the `production` GitHub Environment approval gate, maps Dev $\rightarrow$ Test item GUIDs, and selectively batch-deploys Test $\rightarrow$ Prod.
2. **Fabric Data Pipelines (`fabric-pipelines/`)**:
   - `CICD_Pipeline.json`
   - `PromoteChanges_Pipeline.json`
3. **Azure DevOps YAML Pipelines (`azure-devops/`)**:
   - `azure-pipelines-provision.yml`
   - `azure-pipelines-promote.yml`

## Required GitHub Repository Secrets
Configure under **Settings → Secrets and variables → Actions**:
- `FABRIC_TENANT_ID`: `17f6730e-136d-4e04-baae-83b818a6d047`
- `FABRIC_CLIENT_ID`: `05d9b865-437a-4072-8e5b-8d911e1ef86c`
- `FABRIC_CLIENT_SECRET`: *(Service Principal client secret)*

## Human Approval Gate Setup
Configure under **Settings → Environments → `production`**:
- Enable **Required reviewers** to pause `promote-test-to-prod` until a human reviewer approves the Production deployment.
