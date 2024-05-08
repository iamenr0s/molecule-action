# Molecule Action

This GitHub action is designed to test your Ansible role using Molecule.

Inspired by @robertdebock's [molecule-action](https://github.com/robertdebock/molecule-action/).

## Requirements

This action is compatible with Molecule scenarios employing the podman driver.

For optimal functionality, this action assumes the following structure for your Ansible role (default configuration):
```
.
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── molecule
│   └── default
│       ├── molecule.yml
│       ├── playbook.yml
│       └── prepare.yml
├── requirements.yml
├── tasks
│   └── main.yml
├── tox.ini # OPTIONAL
└── vars
    └── main.yml
```
If a tox.ini file is present, the role is tested using tox. Tox automatically installs all dependencies specified within the tox.ini file, thus determining the version of molecule utilized.

## Inputs

### `namespace`

The Docker Hub namespace where the image is in. Default `"iamenr0s"`.

### `image`

The image you want to run on. Default `"almalinux9"`.

### `tag`

The tag of the container image to use. Default `"latest"`.

### `options`

The [options to pass to `tox`](https://tox.readthedocs.io/en/latest/config.html#tox). For example `parallel`. Default `""`. (empty)

### `command`

The molecule command to use. For example `create`. Default `"test"`.

### `scenario`

The molecule scenario to run. Default `"default"`

## Dependencies

None.

## Example Playbook

```yaml
---
on:
  - push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4
        with:
          path: "${{ github.repository }}"
      - name: molecule
        uses: iamenr0s/molecule-action@main
```

> NOTE:  Please ensure that the checkout action places the file in ${{ github.repository }} for molecule to locate your role correctly.

To test your role against multiple distributions, you can follow this pattern:"

```yaml
---
name: Role-CI

on:
  - push

jobs:
  yamllint:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v4
        with:
          path: "${{ github.repository }}"
      - name: yamllint
        uses: ibiqlik/action-yamllint@v3.1.1
        with:
          config_data: |
            extends: default
            rules:
              new-line-at-end-of-file:
                level: warning
              trailing-spaces:
                level: warning
              line-length:
                level: warning
  test:
    needs:
      - yamllint
    runs-on: ubuntu-latest
    strategy:
      matrix:
        image:
          - alpine
          - amazonlinux
          - debian
          - centos
          - fedora
          - opensuse
          - ubuntu
    steps:
      - name: checkout
        uses: actions/checkout@v4
        with:
          path: "${{ github.repository }}"
      - name: molecule
        uses: iamenr0s/molecule-action@main
        with:
          image: "${{ matrix.image }}"
          options: parallel
          scenario: default
```

## License

This project is licensed under the MIT License.

## Author Information
