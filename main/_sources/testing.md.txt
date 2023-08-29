# Testing

## Continuous integration

The collection is tested with a [molecule](https://github.com/ansible-community/molecule) setup covering the included roles and verifying correct installation and idempotency.
In order to run the molecule tests locally with python 3.9 available, after cloning the repository:

```
pip install yamllint 'molecule[docker]~=3.5.2' ansible-core flake8 ansible-lint voluptuous
molecule test --all
```

## Test playbooks

Sample playbooks are provided in the `playbooks/` directory; to run the playbooks locally (requires a rhel system with python 3.9+, ansible, and systemd) the steps are as follows:

```
TODO
```

