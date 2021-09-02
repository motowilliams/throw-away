# Welcome to my github pages site

> Rename or change the content of this file at `docs/content/index.md`
  to get started creating project documentation

## Project Structure

```
Makefile
docs/
 - content/
    - index.md
 - Dockerfile
 - Makefile
 - mkdocs.yml
```

Your documenation content is placed in `docs/content`. These are your markdown files and images. Standard markdown rules apply here.

At `docs/mkdocs.yml` is your configuration for your <a href="https://www.mkdocs.org/user-guide/configuration/" target="_blank">https://www.mkdocs.org</a>

```
{{ include_file('mkdocs.yml') }}
```

You can update the site attributes via the configuration site via the configuration files entries

```
{{ include_file('mkdocs.yml','',0,5) }}
```

or override them via make
```
make build_docs SITE_NAME="my github pages site"
```

## Building Your Site
This project is configured with a Make and has a few tasks created or added too your existing Makefile.

### Source
```
{{ include_file('Makefile') }}
```

### Build

To build your static site run the following

```
make build_docs
```

### Serve

To serve your static site run the following on a liveload server on `127.0.0.0:8000` run the following

```
make serve_docs
```

Or to run it on an alternate port using the following (replacing 8888 with your desired value)

```
make serve_docs LOCAL_PORT=8888
```

## Built using Docker

This documentation build tool lives in a docker container and is called from a Makefile for local building and serving. You can also use this in CI.

### Source
```
{{ include_file('Dockerfile') }}
```
