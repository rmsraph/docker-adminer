# Adminer on Chainguard PHP

This repository builds a Docker image with the standard Adminer 6 single-file application on Chainguard PHP. It is a community fork of [TimWolla/docker-adminer](https://github.com/TimWolla/docker-adminer), adapted and maintained by [rmsraph](https://github.com/rmsraph). It is not the Docker Official Image and is not maintained by the upstream project.

## Use the image

Set `DOCKERHUB_USERNAME` to your Docker Hub namespace:

```sh
export DOCKERHUB_USERNAME=your-dockerhub-user
docker run --rm -p 8080:8080 "$DOCKERHUB_USERNAME/adminer:6"
```

Open [http://localhost:8080](http://localhost:8080). The image listens on port `8080`.

The image includes the PHP drivers for MySQL/MariaDB, PostgreSQL, SQLite, ODBC, and Microsoft SQL Server via FreeTDS. Configure your database server to accept connections from the container's network.

This image uses the standard Adminer single-file release. The fork-specific entrypoint, design selection, default-server prefill, and plugin-loading behavior are not included.

## Build locally

```sh
docker build -t adminer:6 ./6
docker run --rm -p 8080:8080 adminer:6
```

The `6/Dockerfile` uses a Chainguard PHP 8.5 development image only in the build stage to install extensions and collect their shared-library dependencies. The final stage uses the non-development Chainguard PHP runtime and runs as UID `65532`; the development tools are not included in the published image.

## Publish to Docker Hub

The GitHub Actions workflow builds and tests the image on pushes and pull requests. On pushes to `main`/`master` and version tags (`v*`), it publishes a multi-platform manifest for `linux/amd64` and `linux/arm64` under these tags: the Adminer patch version and `6`, `latest`, plus the `-standalone` aliases and `standalone` (for example, `6.3.0`, `6`, `latest`, `6.3.0-standalone`, `6-standalone`, and `standalone`). The patch-version tags are generated from `ADMINER_VERSION` in `6/Dockerfile`.

Create the `adminer` repository under your Docker Hub namespace before enabling automatic pushes.

Add these repository Actions secrets to enable publishing:

- `DOCKERHUB_USERNAME`: Docker Hub username/organization.
- `DOCKERHUB_TOKEN`: Docker Hub access token with permission to push to the `adminer` repository.

The published image name is `<DOCKERHUB_USERNAME>/adminer`.

## Attribution and licensing

This project is derived from [TimWolla/docker-adminer](https://github.com/TimWolla/docker-adminer); upstream attribution and history are retained. The Adminer software is distributed under Apache License 2.0 or GPL 2.0; its license file is included in the image. The upstream Docker repository does not provide a top-level license for its packaging files, so this repository does not claim a new license for those files. Review the licenses of Adminer and all bundled components when redistributing the image.
