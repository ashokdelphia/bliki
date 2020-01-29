# Grimoire.ca Blog/Wiki

This repository contains the infrastructure for publishing a website, built from a suite of Markdown files and other resources, to Amazon.

## Pre-requisites

You will need:

* [MkDocs](https://mkdocs.org) (`brew install mkdocs`)
* The AWS CLI (`brew install awscli`), logged in on an account with access to the `grimoire.ca` S3 bucket.

## Building

To prepare this site for deployment, run mkdocs from the project's root directory:

```bash
mkdocs build
```

The resulting files will be placed in `site` under the project's root directory, replacing any files already present.

You can also preview the site locally:

```bash
mkdocs serve
```

This will automatically rebuild the site every time the files in `docs` change, and will serve them on a web server at <http://127.0.0.1:8000>.

## Publishing

Once the site is built, it can be published to s3:

```bash
aws s3 sync --delete site/ s3://grimoire.ca/
```
