# terraform_state_artifact 
The [`European-Camping-Group-Data-Team/terraform_state_artifact_gcs`](https://github.com/European-Camping-Group-Data-Team/terraform_state_artifact_gcs) action is a composite action that stores your Terraform state file as a google storage artifact and downloads and the state on subsequent runs. Built-in are the actions: [`hashicorp/setup-terraform@v4`](https://github.com/hashicorp/setup-terraform).

## :rocket: What this action does: :rocket:

- 🛠️ It installs terraform using [`hashicorp/setup-terraform@v4`](https://github.com/hashicorp/setup-terraform).
- :inbox_tray: Using [environment variables](https://docs.github.com/en/actions/learn-github-actions/environment-variables) it downloads the most recent [workflow artifact](https://docs.github.com/en/actions/advanced-guides/storing-workflow-data-as-artifacts) called `terraformstatefile`.
  - If no artifact with that name is found (maybe it's your first run) then it proceeds with the following.
- :building_construction: If no apply action is defined it runs `terraform plan` with any flags from the optional variable `custom_plan_flags`
- 🏢 Next it runs `terraform apply` with any flags from the optional variable`custom_apply_flags`.
  - This can be skipped by setting the optional variable `apply` to `false`.
- 🗃️ If all is well then Terraform has now produced a statefile `./terraform.tfstate`.
- 💾 Finally the new statefile is uploaded as an artifact to google storage!

## Usage v1

```yaml
steps:
- uses: European-Camping-Group-Data-Team/terraform_state_artifact_gcs@v1
    with:
        environment: 'dev'
```

You can choose to skip `terraform apply`:

```yaml
steps:
- uses: European-Camping-Group-Data-Team/terraform_state_artifact_gcs@v1
    with:
        environment: 'dev'
        apply: false
        terraformstatefile: 'terraformstatefile-dev'
```

You can choose to add custom flags to `terraform plan`:

```yaml
steps:
- uses: European-Camping-Group-Data-Team/terraform_state_artifact_gcs@v1
    with:
        environment: 'dev'
        apply: false
        custom_plan_flags: '-refresh-only'
        terraformstatefile: 'terraformstatefile-dev'
```

You can choose to add custom flags to `terraform apply`:

```yaml
steps:
- uses: European-Camping-Group-Data-Team/terraform_state_artifact_gcs@v1
    with:
        environment: 'dev'
        custom_apply_flags: '-no-color'
        terraformstatefile: 'terraformstatefile-dev'
```

## Inputs v1

The action supports the following inputs:

| Variable        | Description                                                                                                                             | Default |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------|---------|
| `encryptionkey` | An encryption key to use when encrypting the statefile. Recommended to use a secret value.                                              |   N/A   |
| `environment`         | Environment to use (dev, staging or prod).               | N/A  |
| `terraformstatefile`         | Terraform statefile to use (terraformstatefile-dev, terraformstatefile-staging, terraformstatefile).               | N/A  |
| `apply`         | (optional) Whether to run the `terraform apply` command.               | `true`  |
| `destroy`       | (optional) Whether to run the `terraform apply -destroy` command.               | `false`  |
| `upload`         | (optional) Upload artifact file.               | `false`  |
| `custom_plan_flags`         | (optional) Add a custom flag to the `terraform plan` command.               | `''`  |
| `custom_apply_flags`         | (optional) Add a custom flag to the `terraform apply` command.               | `''`  |
| `custom_destroy_flags`         | (optional) Add a custom flag to the `terraform apply -destroy` command.               | `''`  |
