# Shellgrunts

This repo contains utility scripts and tools aimed at improving the User experience
in GitOps workflows for IAC or to workaround a limitation or lack of capability in
existing software or services


## Language

### Shellgrunt: sg_emulate_opentofu_early_init

**Does what?:** This Shellgrunt aims to provide backwards compatibility for users and developers who
want or need to maintain compatibility with Hashicorp Terraform who are making use of
the early variable/locals evaluation for backends, module sources and other "init" type
stages such as encryption configuration added in OpenTofu 1.8+

[Read the docs](docs/sg_emulate_opentofu_early_init.md)


## Integrations

### Shellgrunt: sg_gitlab_tfreport_from_tfe

**Does what?:** This Shellgrunt attempts to fetch and analyze Terraform plan information from Terraform
Enterprise (TFE) or Terraform Cloud (TFC). The script generates a GitLab-CI compatible report showing
planned resource changes.

[Read the docs](docs/sg_gitlab_tfreport_from_tfe.md)


## Contributing

If you have one of your own that you'd like to add to the collection please consider making a Pull Request