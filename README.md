*This project has been created as part of the 42 curriculum by rmunoz-c.*

# 🐳 Inception

## 📖 Description

**Inception** is a DevOps project from the 42 curriculum. Its goal is to introduce students to containerization and orchestration using Docker and Docker Compose. The project consists of deploying a secure, multi-service web infrastructure using only Docker, with strict requirements on isolation, reproducibility, and automation.

You will build and configure several essential services (WordPress, MariaDB, Nginx, Redis, FTP, Adminer, Portainer, and a static website) as individual Docker containers, ensuring secure communication, persistent data, and proper service orchestration. The project emphasizes best practices in container security, secret management, and infrastructure-as-code.

---

## 🚀 Instructions

### 1. **Prerequisites**

- **Operating System:** Linux (recommended: Ubuntu 22.04+)
- **Docker:** v20.10+
- **Docker Compose:** v2.0+
- **Make:** (for provided Makefile)

### 2. **Setup**

Clone the repository and enter the project directory:
```sh
git clone https://github.com/<your-login>/inception.git
cd inception
```

### 3. **Secrets & Environment**

Create the required secret files in the `secrets/` directory (one per secret, each containing only the secret value):

- `db_root_password`
- `db_password`
- `wp_admin_password`
- `wp_user_password`
- `ftp_password`
- `portainer_admin_password`

Edit the `.env` file in `srcs/` to set environment variables (database name, user, domain, etc.).

### 4. **Build & Run**

Use the provided Makefile for common operations:

```sh
make upb      # Build and start all services in the background
make logs     # Follow logs for all services
make down     # Stop and remove all containers
make fclean   # Stop, remove containers, images, and volumes
```

Or use Docker Compose directly:

```sh
cd srcs
docker compose up -d --build
```

### 5. **Accessing Services**

- **WordPress:** https://localhost/
- **Adminer:** http://localhost:8081/
- **Static Website:** http://localhost:8082/
- **Portainer:** https://localhost:9443/ (admin password from secrets)
- **FTP:** Port 21 (with passive ports 21100-21110)
- **Redis:** Internal only
- **MariaDB:** Internal only

---

## 🏗️ Project Structure & Design

### **Services**

- **Nginx:** SSL termination, reverse proxy for WordPress and static site
- **WordPress:** PHP-FPM, dynamic content
- **MariaDB:** Database for WordPress
- **Redis:** Caching for WordPress
- **FTP:** File transfer to WordPress volume
- **Adminer:** Database management UI
- **Portainer:** Docker management UI
- **Static:** Simple static website

### **Docker & Sources**

- Each service has its own Dockerfile and configuration under `srcs/requirements/`
- All images are built locally (no pulling from Docker Hub except base images)
- Secrets are managed via Docker secrets (not environment variables)
- Data persistence is ensured via Docker volumes

---

## ⚙️ Main Design Choices

### **Virtual Machines vs Docker**

| Virtual Machines         | Docker Containers           |
|-------------------------|----------------------------|
| Heavyweight, full OS    | Lightweight, share kernel  |
| Slow startup            | Fast startup               |
| Resource intensive      | Efficient resource usage   |
| Harder to reproduce     | Easy to version & deploy   |

**Docker** was chosen for its speed, efficiency, and reproducibility, making it ideal for microservice architectures and CI/CD.

### **Secrets vs Environment Variables**

| Environment Variables         | Docker Secrets                |
|------------------------------|-------------------------------|
| Easy to use                  | More secure                   |
| Exposed in process list/env  | Mounted as files, not in env  |
| Not encrypted                | Isolated from container env   |

**Docker secrets** are used for all sensitive data (passwords), ensuring they are not exposed in environment variables or logs.

### **Docker Network vs Host Network**

| Host Network           | Docker Network (Bridge)      |
|-----------------------|------------------------------|
| Shares host stack     | Isolated, virtual network    |
| Less secure           | Services isolated by default |
| Port conflicts        | No conflicts, mapped ports   |

**Bridge networks** are used for service isolation and controlled communication.

### **Docker Volumes vs Bind Mounts**

| Bind Mounts                | Docker Volumes                |
|----------------------------|-------------------------------|
| Direct host path           | Managed by Docker             |
| Prone to permission issues | Consistent, portable          |
| Not portable               | Easy backup/migration         |

**Docker volumes** are used for persistent data (MariaDB, WordPress, Portainer), ensuring portability and data safety.

---

## ✨ Features

- **Automated multi-service deployment** with Docker Compose
- **SSL/TLS encryption** for all web traffic (self-signed certificates)
- **Isolated, reproducible infrastructure** (no manual steps)
- **Secure secret management** (Docker secrets)
- **Persistent storage** (Docker volumes)
- **Bonus services**: Redis, FTP, Adminer, Portainer, Static site
- **Makefile** for easy management

---

## 📚 Resources

### **Documentation & References**

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [WordPress Docker Image](https://hub.docker.com/_/wordpress)
- [MariaDB Docker Image](https://hub.docker.com/_/mariadb)
- [Nginx Docker Image](https://hub.docker.com/_/nginx)
- [Docker Secrets](https://docs.docker.com/engine/swarm/secrets/)
- [42 Inception Subject PDF](https://cdn.intra.42.fr/pdf/pdf/188979/es.subject.pdf)

### **Use of Artificial Intelligence**

In this project, **GitHub Copilot** and **ChatGPT** were used as assistance tools for:

- Explaining Docker concepts and best practices
- Troubleshooting Docker Compose and service issues
- Optimizing Dockerfiles and entrypoint scripts
- Improving documentation and README formatting

**All code and configurations were reviewed, tested, and adapted by the author to fit the project requirements. AI was used as a support tool, not as an automatic code

