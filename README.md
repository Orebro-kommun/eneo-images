# eneo-images

Builds Örebro kommun's Eneo images from branches in
[Orebro-kommun/eneo](https://github.com/Orebro-kommun/eneo) and publishes them to
`ghcr.io/orebro-kommun/eneo-backend` and `ghcr.io/orebro-kommun/eneo-frontend`.

The build runs here and not in the fork, so the fork can keep GitHub Actions turned
off and stay identical to the project apart from Örebro's own branches.

## Build

Actions → **Build Örebro images** → Run workflow, or:

```bash
gh workflow run build-images.yml --repo Orebro-kommun/eneo-images -f ref=orebro/develop
```

| Branch | Version | Used for |
|---|---|---|
| `orebro/develop` | `develop-<commit in the project's develop>-orebro.<run>` | Test |
| `orebro/release-X.Y` | `<the project's version>-orebro.<run>` | Production |

The run summary lists the version, the commit it is built on and the patches on top
of the project. Both images are scanned with Grype. As in the project, the run
fails on HIGH or CRITICAL vulnerabilities that have a fix in the frontend image,
and only reports them for the backend image.

## Deploy

The project's `docs/deployment/docker-compose.yml` takes the images from the
environment, so the compose file is used unchanged:

```dotenv
ENEO_VERSION=<the version from the run summary>
ENEO_BACKEND_IMAGE=ghcr.io/orebro-kommun/eneo-backend:<version>
ENEO_FRONTEND_IMAGE=ghcr.io/orebro-kommun/eneo-frontend:<version>
```

The tool runtime and SeaweedFS images are the project's own.
