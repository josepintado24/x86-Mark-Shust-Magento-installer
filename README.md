# Magento x86 Installer

Clean x86_64/amd64 Docker installer for a Magento project.

This package does not include the Magento source code. Clone the Magento project into `src`.

## Versions

- Mark Shust Docker Magento base: `52.1.0`
- Nginx: `markoshust/magento-nginx:1.28-0`
- PHP-FPM: `markoshust/magento-php:8.3-fpm-7`
- PHP-FPM Xdebug: `markoshust/magento-php:8.3-fpm-xdebug-7`
- MariaDB: `10.4.3`
- Redis-compatible cache: `valkey/valkey:8.1-alpine`
- Elasticsearch: `markoshust/magento-elasticsearch:7.17-1`
- RabbitMQ: `markoshust/magento-rabbitmq:4.2-0`
- Mailcatcher: `sj26/mailcatcher:v0.10.0`
- Target platform: `linux/amd64`

The Magento application version comes from the cloned project `src/composer.json`. The current expected version is:

- `magento/product-enterprise-edition`: `2.4.7-p3`

## Requirements

- Docker Desktop or Docker Engine with Docker Compose.
- At least 6 GB RAM assigned to Docker.
- Magento Marketplace credentials available in Composer auth.
- The Magento project repository cloned into `src`.

## Setup

If this folder was copied manually and scripts fail with `Permission denied`, run this command in your terminal from the installer root. Do not paste it only into this README; it must be executed:

```bash
chmod +x bin/*
```

Then run the script again:

```bash
bin/start --no-dev
```

If it still fails, force permissions recursively:

```bash
find bin -type f -exec chmod 755 {} \;
```

Create local env files from the included examples:

```bash
for file in env/*.env.example; do cp "$file" "${file%.example}"; done
```

Review the generated files in `env/*.env`, especially:

- `env/magento.env`
- `env/db.env`
- `env/elasticsearch.env`

Clone the Magento project into `src`:

```bash
git clone <project-repository-url> src
```

Start the containers:

```bash
bin/start
```

Install Composer dependencies inside the PHP container:

```bash
bin/composer install
```

Install Magento:

```bash
bin/setup-install magento.test
```

Run the standard Mark setup flow only if you want a fresh install managed by the Mark scripts:

```bash
bin/setup magento.test
```

If you already cloned the project into `src`, do not run `bin/setup` without reviewing it first because the Mark setup script recreates `src`.

## Useful Commands

```bash
bin/magento cache:flush
bin/magento indexer:reindex
bin/composer install
bin/cli bash
bin/stop
```

## Hostname

Add this entry to your hosts file if needed:

```text
127.0.0.1 magento.test
```

## Important Notes

- This installer intentionally does not delete or recreate `src`.
- This installer uses Elasticsearch 7, not OpenSearch.
- This installer does not include the ARM Java workaround `_JAVA_OPTIONS=-XX:UseSVE=0`.
- `platform: linux/amd64` is pinned on services so shared x86 installs resolve the same architecture.
- `env/*.env` files are local and ignored by Git. Share only `env/*.env.example` unless secrets have been removed.

## Included Mark-Style Files

- `compose.yaml` - main services.
- `compose.healthcheck.yaml` - service healthchecks and dependencies.
- `compose.dev.yaml` - default local source mounts.
- `compose.dev-linux.yaml` - Linux-style full `src` mount, kept for Mark compatibility.
- `compose.dev-ssh.yaml` - optional SSH service, kept for Mark compatibility.
- `template/dev/tests/integration/etc/install-config-mysql.php.2.4.dist` - Magento 2.4 integration test install template.
- `template/dev/tests/integration/etc/install-config-mysql.php.2.3.dist` - Magento 2.3 integration test install template kept for Mark compatibility.
- `Makefile` - command shortcuts for the included safe scripts.

## Compose Usage

Default development mounts:

```bash
bin/start
```

No dev mounts:

```bash
bin/docker-compose --no-dev up -d --remove-orphans
```

To use `compose.dev-linux.yaml` or `compose.dev-ssh.yaml`, call Docker Compose directly with the desired files, matching Mark Shust's multi-file style.
