# sfdx_mallow

Salesforce DevOps pipeline built with [sfdx-hardis](https://sfdx-hardis.cloudity.com), to evaluate the tool for our organisation.

## Branches and orgs

Each major branch deploys to one Salesforce org:

- `dev` deploys to the Dev sandbox. Every User Story starts from it and comes back to it.
- `uat` deploys to the UAT sandbox. Work reaches it only by promotion from `dev`.
- `main` deploys to Production. Work reaches it only by promotion from `uat`.

The branch settings are in `config/branches/.sfdx-hardis.<branch>.yml`, and the project settings in `config/.sfdx-hardis.yml`.

## How a change reaches production

1. In VS Code, open the sfdx-hardis extension and run **New User Story**. It creates a branch from `dev`.
2. Make the change in the Dev sandbox, then run **Save User Story**. It retrieves the metadata, updates `manifest/package.xml` and pushes the branch.
3. Open a Pull Request into `dev`. The **Simulate Deployment to Major Org** and **Mega-Linter** checks must pass before the merge.
4. Merging deploys to the Dev sandbox. Promote with a Pull Request from `dev` into `uat`, then from `uat` into `main`: each one is checked against its org, then deployed when merged.

Direct pushes to `dev`, `uat` and `main` are blocked by branch protection.

## CI authentication

The CI jobs connect to each org with an External Client App and a JWT certificate. The encrypted keys are in `config/branches/.jwt/`, and the GitHub secrets `SFDX_CLIENT_ID_<BRANCH>` and `SFDX_CLIENT_KEY_<BRANCH>` hold the matching Consumer Key and decryption key.

After a sandbox refresh, run `sf hardis:project:configure:auth` again for that branch and update its secrets.

## Further reading

- [sfdx-hardis DevOps setup guide](https://sfdx-hardis.cloudity.com/salesforce-devops-setup-home/)
- [sfdx-hardis training, Release Manager level](https://sfdx-hardis-training.cloudity.com/en/level-3-release-manager/)
