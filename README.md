# ChRIS Store Action

A Github Action for publishing a ChRIS plugin JSON descriptor file
to a ChRIS store, and optionally, to a ChRIS backend as well.

## Example

```yaml
name: publish

on:
  push:
    tags: [ '**' ]

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - name: Create JSON descriptor file
        run: podman run --rm ghcr.io/fnndsc/pl-myappname:1.2.3 chris_plugin_info > /tmp/App.json
      - name: Upload to ChRIS Store
        id: chrisstore
        uses: FNNDSC/chrisstore-action@master
        with:
          # ChRIS Store information
          name: pl-myappname
          descriptor_file: /tmp/App.json
          dock_image: ghcr.io/fnndsc/pl-myappname:1.2.3
          public_repo: https://github.com/FNNDSC/myExampleRepo
          auth: ${{ secrets.CHRIS_STORE_USER }}
          url: https://chrisstore.co/api/v1/

          # Optional ChRIS backend admin information
          chris_admin_auth: ${{ secrets.CUBE_CHRISPROJECT_ORG_ADMIN_USER }}
          chris_admin_url: https://cube.chrisproject.org/chris-admin/api/v1/
          compute_resources: host,moc  # comma-separated list of names
```

## Options

`descriptor_file` is a path to a JSON file representation of your _ChRIS_ plugin's metadata.

`auth` is the login credentials of your _ChRIS Store_ user, in the form `username:password`. It is recommended to store it as a
[secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets).

The default value for `url` is `https://chrisstore.co/api/v1/`.

`name`, `dock_image`, and `public_repo` are optional. If not specified,
their values will be inferred from the name of the Github repository.
But as a caveat, `dock_image` can only be inferred when the action's ref is a tag.
Moreover, this action will enforce that `dock_image` is tagged by version,
and that `dock_image` does not end with `:latest`.

### Minimal Example

```yaml
name: publish

on:
  push:
    tags: [ '**' ]

jobs:
  upload:
    runs-on: ubuntu-latest
    steps:
      - name: Create JSON descriptor file
        run: podman run --rm ghcr.io/fnndsc/pl-myappname:1.2.3 chris_plugin_info > /tmp/App.json
      - name: Upload to ChRIS Store
        id: chrisstore
        uses: FNNDSC/chrisstore-action@master
        with:
          descriptor_file: /tmp/App.json
          auth: ${{ secrets.CHRIS_STORE_USER }}
```
