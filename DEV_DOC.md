*This project has been created as part of the 42 curriculum by rmunoz-c.*

# DEV_DOC — Inception (Developer Documentation)

This document provides all the information needed for a developer to set up, build, launch, and manage the Inception project from scratch, including environment setup, configuration, secret management, container/volume operations, and data persistence.

---

## 1. Environment Setup (from scratch)

### **Prerequisites**

- Linux OS (recommended: Ubuntu 22.04+)
- Docker v20.10+  
  [Install Docker](https://docs.docker.com/engine/install/)
- Docker Compose v2.0+  
  [Install Docker Compose](https://docs.docker.com/compose/install/)
- GNU Make (for Makefile usage)
- Git

### **Repository Structure**

```
inception/
├── Makefile
├── README.md
├── USER_DOC.md
├── DEV_DOC.md
├── secrets/
│   ├── db_root_password
│   ├── db_password
│   ├── wp_admin_password
│   ├── wp_user_password
│   ├── ftp_password
│   └── portainer_admin_password
└── srcs/
    ├── .env
    ├── docker-compose.yml
    └── requirements/
        └── ... (service folders and Dockerfiles)
```

---

## 2. Configuration Files & Secrets

### **.env file**

Located at `srcs/.env`.  
Contains non-sensitive configuration (database name, user, domain, etc.).  
Edit this file as needed before launching the stack.

Example content:
```
MYSQL_DATABASE=wordpress
MYSQL_USER=wp_user
DOMAIN=localhost
...
```

### **Secrets**

All sensitive credentials are stored as individual files in the `secrets/` directory at the project root.  
**Each file must contain only the secret value, no extra spaces or newlines.**

Required files:
- `db_root_password`
- `db_password`
- `wp_admin_password`
- `wp_user_password`
- `ftp_password`
- `portainer_admin_password`

Example (to create a secret file):
```sh
echo "your_strong_password" > secrets/db_root_password
chmod 600 secrets/db_root_password
```

---

## 3. Build and Launch the Project

### **Using the Makefile (recommended)**

From the project root:

- Build and start all services:
  ```sh
  make upb
  ```

- Start all services (no build):
  ```sh
  make up
  ```

- Stop and remove all containers:
  ```sh
  make down
  ```

- Remove containers, images, and volumes:
  ```sh
  make fclean
  ```

- View logs for all services:
  ```sh
  make logs
  ```

- Build and start a specific service (example: nginx):
  ```sh
  make upb-nginx
  ```

### **Using Docker Compose directly**

From the `srcs/` directory:

- Build and start:
  ```sh
  docker compose up -d --build
  ```

- Stop and remove:
  ```sh
  docker compose down -v
  ```

---

## 4. Managing Containers and Volumes

### **Containers**

- List running containers:
  ```sh
  docker ps
  ```

- List all containers (including stopped):
  ```sh
  docker ps -a
  ```

- Restart a service:
  ```sh
  docker compose -f srcs/docker-compose.yml restart <service>
  ```

- View logs for a service:
  ```sh
  docker compose -f srcs/docker-compose.yml logs -f <service>
  ```

- Execute a shell inside a running container (example: wordpress):
  ```sh
  docker exec -it wordpress /bin/sh
  ```

### **Volumes**

- List Docker volumes:
  ```sh
  docker volume ls
  ```

- Inspect a volume:
  ```sh
  docker volume inspect inception_wordpress_data
  ```

- Remove a volume (be careful, this deletes data!):
  ```sh
  docker volume rm inception_wordpress_data
  ```

- Clean up unused volumes:
  ```sh
  docker volume prune
  ```

---

## 5. Data Persistence

### **Where is data stored?**

- **MariaDB data:**  
  Docker volume: `mariadb_data`  
  Path inside container: `/var/lib/mysql`

- **WordPress data:**  
  Docker volume: `wordpress_data`  
  Path inside container: `/var/www/html`

- **Portainer data:**  
  Docker volume: `portainer_data`  
  Path inside container: `/data`

These Docker volumes are defined in `srcs/docker-compose.yml` and are managed by Docker.  
**Data in these volumes persists across container restarts and rebuilds.**

To back up a volume:
```sh
docker run --rm -v inception_mariadb_data:/data -v $(pwd):/backup alpine tar czf /backup/mariadb_data.tar.gz -C /data .
```

To restore:
```sh
docker run --rm -v inception_mariadb_data:/data -v $(pwd):/backup alpine tar xzf /backup/mariadb_data.tar.gz -C /data
```

---

## 6. Troubleshooting & Tips

- Always check that Docker and Docker Compose are running and up to date.
- If a service fails, check its logs and the health status.
- Permissions issues with volumes can often be fixed by recreating the volume and restarting the service.
- If you edit Dockerfiles or configuration, rebuild the affected service:
  ```sh
  make build-<service>
  make up-<service>
  ```
- To reset everything (including data):  
  ```sh
  make fclean
  ```

---

## 7. Useful References

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [42 Inception Subject PDF](https://cdn.intra.42.fr/pdf/pdf/188979/es.subject.pdf)

---

This DEV_DOC provides all necessary information for developers to configure, build, run, and maintain the Inception