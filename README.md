# Introduction

This tool allows for loading of environment variables similar to 
`symfony/dotenv`, only then for Docker Compose environments. Loading 
environment variable this way should help with a more consistent development
process.

# Installation

Checkout the project and add `dev` to `$PATH`.

# Using .local domains for databases
This tool uses `avahi` and `nss-mdns` to assign database IP's to domain names,
so they can be easily accessed in a development environment. In order to use 
`.local` domains for databases, edit the following files:

Edit: `/etc/nsswitch.conf`
Edit the hosts line to match the following:
```
hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4
```

Create/edit: `/etc/mdns.allow`
Add the following line:
```
.local
```

# Usage

Ensure `docker-compose.yaml` (and optionally `.env` and `.env.dev`) is 
available and configured. Then start the environment with `dev up`.

## Install a templated environment

To install a templated environments from this project use `dev init`.

## Add templated services

Adding a `templated-services.yaml` in the project root (same path as
`docker-compose.yaml`) allows multiple services to start based on a template
with a list of variables. The structure of `templated-services.yaml` is:

```yaml
templates:
  example:
    template: example-template
    environment:
      name: example-service
      image: example-docker-image:latest
      ...
  ...
```

The `templates.*` nodes define a configuration for loading in addition to
`docker-compose.yaml`.

The `templates.*.template.*` nodes define the template for the configuration. 
The value will be used to load a Docker Compose compatible file (with variables
from the configuration). The template name has to be defined in the root of the
project and the example above is translated to the following filename:
`docker-compose.example-template.tpl.yaml`.

The `templates.*.environment.*` node define environment variables that are used
when loading the template defined in the `template` sibling node. Note that
some environment variables can not be used and will be ignored, because they 
are used internally:

* `id`
* `id_vars`
* `pwd`
* `template`
* `ts_file`
* `tsdc_file`
* `vars`

# Update environment templates

When updating files for a certain template, like magento-2, ensure to update
the `version` of `docker-compose.yaml`. For example:

```yaml
x-custom:
  version: 1.x.x
  type: magento-2
```

When updating a template in a project, ensure any customization are reverted.
