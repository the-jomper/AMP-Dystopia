# Dystopia AMP template

Linux template for the AMP Generic Module. Experimental: not yet tested in a live AMP instance.

## Installation through GitHub

Repository: `the-jomper/AMP-Dystopia`, branch `main`.

All template files and `manifest.json` must be at the repository root, not inside a subdirectory or ZIP file.

In AMP, open **Configuration → Instance Deployment → Configuration Repositories**, add:

```
the-jomper/AMP-Dystopia:main
```

Keep `CubeCoders/AMPTemplates:main` in the list. Click **Fetch Latest**, refresh the browser, and create an instance using the Dystopia template from this repository.

If you use a different repository name or owner, update `origin` and `url` in `manifest.json` and the entry above.

See [the Danish installation and test guide](INSTALLATION-DA.md) for server settings, ports and limitations.

Based on CubeCoders/AMPTemplates under the included MIT license.

