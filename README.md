# magento-nginx

Nginx Docker image pre-configured for Magento 2 deployments, based on `nginx:alpine`.
Runs as a non-root user and includes a ready-to-use `nginx.conf` with the image filter
module enabled.

Available on:
- **Docker Hub**: [`collabpl/magento-nginx`](https://hub.docker.com/r/collabpl/magento-nginx)
- **GitLab Registry**: `gitlab-registry.collab.pl/collab/docker/magento-nginx`

## Available tags

| Tag | Base image | Nginx |
|-----|-----------|-------|
| `1.24` | `nginx:1.24-alpine` | 1.24 |

## What's included

- **Non-root user**: `www` (UID/GID `1000`, configurable via the `APP_ID` build arg)
- **Magento root**: `/var/www/html` (configurable via the `MAGENTO_ROOT` build arg)
- **`/sock`**: directory for PHP-FPM unix socket communication
- **Custom `nginx.conf`**: loads `ngx_http_image_filter_module`, sets up access/error
  logging, and includes `/etc/nginx/conf.d/*.conf` for virtual host configuration
- Exposes **port 80**

## Usage

### Docker Hub

```bash
docker pull collabpl/magento-nginx:1.24
```

### GitLab Registry

```bash
docker pull gitlab-registry.collab.pl/collab/docker/magento-nginx:1.24
```

### docker-compose example

```yaml
services:
  nginx:
    image: gitlab-registry.collab.pl/collab/docker/magento-nginx:1.24
    ports:
      - "80:80"
    volumes:
      - ./src:/var/www/html
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
```

### Custom build args

```bash
docker build \
  --build-arg APP_ID=1001 \
  --build-arg MAGENTO_ROOT=/var/www/magento \
  -t my-magento-nginx \
  1.24/
```

## Repository structure

```
.
├── 1.24/
│   ├── Dockerfile
│   └── conf/
│       └── nginx.conf
├── .github/
│   └── workflows/
│       └── build-nginx-1.24.yml   # GitHub Actions — build & push to Docker Hub
└── .gitlab-ci.yml                 # GitLab CI — build via Kaniko
```

## CI/CD

**GitHub Actions** builds for both `linux/amd64` and `linux/arm64` and pushes to
Docker Hub on every push to `main` and on published releases. Requires
`DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` repository secrets.

**GitLab CI** uses the internal
[`kaniko-build`](https://gitlab.collab.pl/collab/components/kaniko-build)
component to build and push to the GitLab Container Registry.

## Adding a new nginx version

1. Create a new directory `{version}/` with a `Dockerfile` and `conf/nginx.conf`.
2. Add a new GitHub Actions workflow `.github/workflows/build-nginx-{version}.yml`
   (copy an existing one and update the version references).
3. Add the new tag to the `.gitlab-ci.yml` parallel matrix.
4. Push — both pipelines will build and publish the new image automatically.

## License

MIT
