# Summary

This Shellgrunt attempts to fetch and analyze Terraform plan information from Terraform
Enterprise (TFE) or Terraform Cloud (TFC). The script generates a GitLab-CI compatible
report showing planned resource changes.

**Usage**

```bash
gitlab_ci_report_from_tfe_plan
```

## How it works

There is no currently no convenient method to extract a redacted JSON format plan for
a 'run' in TFE when using the remote state backend.

What we can do though is use the TFE API to make a best guess effort to sequentially work
our way to the JSON format plan for the most recent 'run' associated with our current TFE
workspace name;

- Map the workspace name to it's workspace ID
- Find the latest run for the given workspace ID on the basis that in our CI pipeline we have just triggered speculative run with an associated plan
- Find the Terraform plan linked to the specified run
- Download a redacted version in JSON format of the plan
- Run the JSON through a basic JQ based transformer to create a file that is compatible with Gitlab-CI's terraform report format

Required Environment Variables:

- `TFE_TOKEN` Terraform Enterprise API token. Example: "abcdef123456..."
- `TFE_ORG` Terraform Enterprise organization name. Example: "my-organization"
- `WORKSPACE_NAME` Name of the Terraform workspace to analyze. Example: "production-infrastructure"

Optional Environment Variables:

- `TFE_URL` Terraform Enterprise host URL (defaults to app.terraform.io). Example: "tfe.company.com"


**Example Usage:**

```bash
export TFE_TOKEN="your-token-here"
export TFE_ORG="your-org"
export WORKSPACE_NAME="your-workspace"
./gitlab_ci_report_from_tfe_plan
```

The script will output a JSON report with counts of resources to be created,
updated, or deleted according to the latest Terraform plan in the specified workspace.

**Example Output:**

```json
{
  "create": 2,
  "update": 1,
  "delete": 0
}
```



## FAQ

**Q: Why would I want to use this?**

This will allow you to generate Gitlab-CI pipeline reports for the IAC managed by TFE/TFC which can be very useful when making use of GitOps workflows to process changes
