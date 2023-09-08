# community.fqcn_migration

<!--start build_status -->
[![Build Status](https://github.com/ansible-collections/community.fqcn_migration/workflows/CI/badge.svg?branch=main)](https://github.com/ansible-collections/community.fqcn_migration/actions/workflows/ci.yml)
<!--end build_status -->

This project, called fqcn_migration, is a set of Ansible roles designed to rename a collection and even changed its namespace. The original is refered as upstream and the targeted name as downstream. A use case for this tool would migrating a collection named play_helpers from the community namespace into the ansible one, with the name helpers.

The collection fqcn_migration takes care of the changes required, ensuring that the downstream collections is identical to the upstream, apart for the required naming changes.

## How to install the fqcn_migration collection?

<!--start galaxy_download -->
Like any other Ansible collection!

    $ ansible-galaxy collection install community.fqcn_migration
<!--end galaxy_download -->

## Usage

Suppose you have an upstream collection called **isawesome** living in the namespace **mystuff**. So the fqcn of your collection is **mystuff.isawesome** (see what I did there?). Now, you want to generate a downstream version to deliver certified content to your customers. Your company is **thisisserious** and the downstream of your collection is now **stuff**, so the fqcn is **thisisserious.stuff** (yes, I'm sticking with it). The upstream collection is living in the **mystuff** organiation on github.com and the project is called **isawesome_collection**.

Here is an example playbook to use fqcn_migration to generate the downstream collection (thisisserious.stuff) from the upstream one (mystuff.isawesome):

```yaml
- import_playbook: fqcn_migration.yml
  vars:
    upstream_name: isawesome
    upstream_namespace: mystuff
    downstream_name: stuff
    downstream_namespace: thisisserious
```

### Additonal features

#### Galaxy links

It is possible to transforms links in galaxy.yml using the following dict:

```yaml
galaxy:
  documentation: https://access.redhat.com/documentation/en-us/red_hat_amq_broker
  homepage: https://access.redhat.com/products/red-hat-amq
  repository: ...
  documentation: ...
```


#### Post-processing reverts

Sometimes the transformation applies unwanted replacements. The `post_processors_replacements` list can be used to revert those changes; it has the format:

```yaml
post_processors_replacements:
  - match: "- amq_broker"
    replace: "- activemq"
    file: "meta/main.yml$"
  - match: "amq_broker[.]apache[.]org"
    replace: "activemq.apache.org"
    file: "README.md$"
```

where for each dict in the list:

* `match`: regular expression to lookup
* `replace`: text for replacing the matched string
* `file`: regular expression matching the file paths to operate on

_**Note**_: the the post_processors_replacements variables allow fqcn_migration user to specifiy strings that needs to be replace **after** the transformation process. As illustrated in the example above, the rewriting to content to match the downstream_namespace.downstream_collection_name can sometime transform content that needs to stay the same or replace differently.


#### MarkDown templates

It is possible to perform replacements in markdown files that are templated with:

```markdown
<!--start <placeholder_string> -->
arbitrary text
<!--end <placeholder_string> -->
```

_**NOTE**_: placeholders can NOT be nested; in that case, the outer placeholder takes precedence.

With the `downstream_placeholder_delete` list(str) it is possible to delete markdown section like for instance:

```yaml
downstream_placeholder_delete:
  - build_status
```

against:

```markdown
<!--start build_status -->
[![Build Status](https://github.com/ansible-middleware/amq/workflows/CI/badge.svg?branch=main)](https://github.com/ansible-middleware/amq/actions/workflows/ci.yml)
<!--end build_status -->
```

will hide the github build status link and image in the generated collection.

Similarly, the `downstream_placeholder_content` allow to replace the contents between the placeholders with other markdown, ie:

```yaml
downstream_placeholder_content:
  - placeholder: galaxy_download
    content: |
      ### Installing the Collection from Automation Hub

      Before using the collection, you need to setup Ansible Automation Hub as galaxy server; then install it via the CLI:

          ansible-galaxy collection install redhat.amq_broker
```

against:

```markdown
<!--start galaxy_download -->
### Installing the Collection from Ansible Galaxy

    ansible-galaxy collection install middleware_automation.amq

<!--end galaxy_download -->
```

Will provide different instructions depending on the galaxy server used.


#### Collections dependency map

Sometimes while transforming a collection, it is necessary to transform its dependencies FQCN as well. It is possible to define dependency maps like follows:

```yaml
upstream_downstream_collections_map:
  - { upstream_name: 'middleware_automation.common', downstream_name: 'redhat.runtimes_common' }
```
which will transform occurrences of middleware_automation.common to redhat.runtimes_common in tasks files, galaxy.yml and requirements.yml


<!--start support -->
<!--end support -->

## License

[Apache License 2.0](https://github.com/ansible-middleware/amq/blob/main/LICENSE)
