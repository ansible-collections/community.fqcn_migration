# README - fqcn_migration

This project, called fqcn_migration, is a set of Ansible roles designed to rename a collection and even changed its namespace. The original is refered as upstream and the targeted name as downstream. A use case for this tool would migrating a collection named play_helpers from the community namespace into the ansible one, with the name helpers.

The collection fqcn_migration takes care of the changes required, ensuring that the downstream collections is identical to the upstream, apart for the required naming changes.

## How to install the fqcn_migration collection?

Like any other Ansible collection!

$ ansible-galaxy collection install community.fqcn_migration

## How to use fqcn_migration?

Suppose you have an upstream collection called **isawesome** living in the namespace **mystuff**. So the fqcn of your collection is **mystuff.isawesome** (see what I did there?). Now, you want to generate a downstream version to deliver certified content to your customers. Your company is **thisisserious** and the downstream of your collection is now **stuff**, so the fqcn is **thisisserious.stuff** (yes, I'm sticking with it). The upstream collection is living in the **mystuff** organiation on github.com and the project is called **isawesome_collection**.

Here is an example playbook to use fqcn_migration to generate the downstream collection (thisisserious.stuff) from the upstream one (mystuff.isawesome):

```
---
- import_playbook: fqcn_migration.yml
  vars:
    upstream_name: isawesome
    upstream_namespace: mystuff
    downstream_name: stuff
    downstream_namespace: thisisserious
    post_processors_replacements:
     - match: "http://github.com/thisisserious/stuff"
       replace: "http://github.com/mystuff/isawesome"
```

Note the the post_processors_replacements variables allow fqcn_migration user to specifiy strings that needs to be replace **after** the transformation process. As illustrated in the example above, the rewriting to content to match the downstream_namespace.downstream_collection_name can sometime transform content that needs to stay the same or replace differently.
