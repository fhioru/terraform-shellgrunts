# Summary

> OpenTofu 1.8 brings a much requested feature to allow early variable/locals evaluation
> for backends, module sources and other "init" type stages such as encryption configuration.

This Shellgrunt aims to be a very simple utility script to provide backwards compatibility
for users and developers who want or need to maintain compatibility with either

- Earlier versions of OpenTofu prior to 1.8  (doesn't have this feature)
- Hashicorp Terraform versions 1.x+ (may have the feature but implemented differently)

This allows for a very primitive but functional approach without the need to add or depend
on additional binary tools or services in your IAC workflow that may not be present in
the Docker container, agent or CICD available.

**Usage**

```bash
sg_emulate_opentofu_early_init <manifest.ini> <env-id> [<path>]
```

## How it works

The utility script finds and replaces placeholders within Terraform source files
by loading a series of environments from an INI format file (`<manifest.ini>`) and replacing the
listed module versions matching the specified `<env-id>`.

If `<path>` is not provided it will default to the current directory.

Replacements are performed using an approach that will allow the code to be
written as OpenTofu 1.8 compatible code and support making changes for previous
OpenTofu versions and Hashicorp Terraform

**Example `manifest.ini`**

```ini
[development]
module_encryption_key = "2.2.5"
module_managed_object_storage = "2.0.3"
module_secret_management = "2.1.0"
module_stateful_vm = "0.3.0"
```

Searches for:

```hcl
local . basename(<manifest.ini>) . <env-id> . module_encryption_key
local . basename(<manifest.ini>) . <env-id> . module_managed_object_storage
local . basename(<manifest.ini>) . <env-id> . module_secret_management
local . basename(<manifest.ini>) . <env-id> . module_stateful_vm
```

Replaces each with the corresponding value in the INI file.

```hcl
local.manifest.development.module_encryption_key         => "2.2.5"
local.manifest.development.module_managed_object_storage => "2.0.3"
local.manifest.development.module_secret_management      => "2.1.0"
local.manifest.development.module_stateful_vm            => "0.3.0"
```

Allowing the code

```hcl
module "module_stateful_vm" {
  source             = "registry.example.io/module_encryption_key/aws"
  version            = local.tf_module_versions_by_env.development.module_encryption_key
  configuration_file = "./example.yml"
}
```

To become

```hcl
module "module_stateful_vm" {
  source             = "registry.example.io/module_encryption_key/aws"
  version            = "2.2.5"
  configuration_file = "./example.yml"
}
```

## FAQ

**Q: Why not use [Terragrunt](https://terragrunt.gruntwork.io/) which already supports this?**

In a situation where you need this type of functionality (find+replace) and can add or bake in Terragrunt then using Terragrunt will likely be a much better solution until such time as the functionality is present in your preferred IAC toolset of choice

**Q: Why would I want to use this?**

This will allow you to write code that is OpenTofu 1.8+ compatible and still support Hashicorp/IBM Terraform
