*This project has been created as part of the 42 curriculum by rmunoz-c.*

# USER_DOC — Inception (User Documentation)

This document explains, in simple terms, what services the Inception stack provides, how to start and stop it, how to access the website and admin panels, where to find credentials, and how to check that services are running correctly.

---

## Services provided by the stack

The Inception stack deploys a small web infrastructure composed of the following services (see srcs/docker-compose.yml):

- WordPress (PHP-FPM) — web application (HTTPS)
- Nginx — SSL termination and reverse proxy for WordPress and the static site
- MariaDB — database for WordPress
- Redis — caching service (internal)
- FTP (vsftpd) — file upload to WordPress volume (passive ports configured)
- Adminer — database management UI
- Portainer — Docker management UI
- Static — simple static website

Which services are reachable from the host and on which ports:
- WordPress (via nginx/HTTPS): 443 -> https://localhost/
- Adminer: 8081 -> http://localhost:8081/
- Static website: 8082 -> http://localhost:8082/
- Portainer: 9443 (HTTPS) and 9000 (HTTP) -> https://localhost:9443/ or http://localhost:9000/
- FTP: 21 (control) and passive 21100-21110
- MariaDB / Redis: internal only (not exposed to host)

---

## Start and stop the project

Recommended: use the provided Makefile at repository root (Linux).

From the repository root:

- Build and start all services (build images and run in background):
  ```sh
  make upb
  ```

- Start all already-built services (no build):
  ```sh
  make up
  ```

- Build + start a single service (example: wordpress):
  ```sh
  make upb-wordpress
  ```

- Build ftp and start without its compose depends:
  ```sh
  make upb-ftp
  ```

- Stop and remove all containers (keep images and volumes):
  ```sh
  make down
  ```

- Stop, remove containers, images and volumes:
  ```sh
  make fclean
  ```

You can also use docker compose directly from srcs:
```sh
cd srcs
docker compose up -d --build
docker compose down -v
```

If Docker service is not running, start it (Linux):
```sh
sudo systemctl start docker
```
or restart if needed:
```sh
sudo systemctl restart docker
```

---

## Accessing the website and admin panels

Open these URLs in your browser (use localhost or the host IP):

- WordPress (public site): https://localhost/  
  Note: the project uses self-signed certificates by default — the browser will warn about the certificate.

- Adminer (DB admin UI): http://localhost:8081/  
  Use DB credentials from the secrets directory (see below).

- Static site: http://localhost:8082/

- Portainer (Docker UI): https://localhost:9443/ (or http://localhost:9000/)  
  Use the Portainer admin password stored in secrets/portainer_admin_password.

- FTP: connect to host IP on port 21. Configure passive ports 21100–21110 in your FTP client and use the user/password from secrets/ftp_password.

---

## Locate and manage credentials

All sensitive values (passwords) are stored as files in the repository `secrets/` directory (one file per secret). This project uses Docker secrets; the secrets directory must exist at repository root.

Secrets files:
- secrets/db_root_password
- secrets/db_password
- secrets/wp_admin_password
- secrets/wp_user_password
- secrets/ftp_password
- secrets/portainer_admin_password

To inspect a secret locally (careful: exposes the secret in your terminal):
```sh
cat secrets/db_password
```

Permissions tip: keep secret files readable only by your user:
```sh
chmod 600 secrets/*
```

Environment variables are read from `srcs/.env`. This file contains non-sensitive configuration (DB name, user names, domain, etc.) — edit it before starting if needed.

Security reminder: do NOT commit real production secrets to Git. Use meaningful but safe test passwords for school projects.

---

## Check that services are running correctly

Useful commands (run from repository root on Linux):

- Show running containers and status (compose):
  ```sh
  docker compose -f srcs/docker-compose.yml ps
  ```

- Show all running containers:
  ```sh
  docker ps
  ```

- Follow logs for all services:
  ```sh
  make logs
  # or
  docker compose -f srcs/docker-compose.yml logs -f
  ```

- Follow logs for a single service (example: mariadb):
  ```sh
  docker compose -f srcs/docker-compose.yml logs -f mariadb
  ```

- Inspect container health status (example: mariadb):
  ```sh
  docker inspect --format='{{json .State.Health}}' mariadb
  ```

- Check a service port from the host (example: nginx HTTPS):
  ```sh
  curl -k -I https://localhost/
  ```

Common indicators of healthy operation:
- mariadb container shows HEALTHY (compose healthcheck)
- wordpress logs show successful DB connection
- nginx serves index pages (curl returns HTTP 200/301/302)
- Portainer and Adminer are reachable on their ports

---

## Quick troubleshooting

- Docker daemon not running:
  ```sh
  sudo systemctl status docker
  sudo systemctl restart docker
  ```

- If compose fails to start with a daemon error, check /etc/docker/daemon.json for JSON syntax errors (missing quotes, trailing commas). Fix the file and restart docker.

- If a service exits immediately, inspect its logs:
  ```sh
  docker compose -f srcs/docker-compose.yml logs -f <service>
  ```

- If volumes produce permission errors, stop containers and recreate volumes:
  ```sh
  make down
  docker volume ls
  docker volume rm inception_mariadb_data inception_wordpress_data || true
  make upb
  ```

- To completely reset the environment (removes volumes and images):
  ```sh
  make fclean
  ```

---

## Where to find help

- Review logs (make logs) and container output for error messages.
- Check secret files and srcs/.env for missing or empty values.
- Verify Docker & Docker Compose versions:
  ```sh
  docker --version
  docker compose version
  ```

---

This USER_DOC contains the essential information for an end user or administrator to run, access and verify the Inception stack. For developer-level instructions, see DEV_DOC.md at