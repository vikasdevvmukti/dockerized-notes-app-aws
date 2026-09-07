# 🐳 Dockerized Notes App with MongoDB — Deployed on AWS with SSL

**Objective:** Containerize a full-stack Node.js + MongoDB application using Docker and Docker Compose, then deploy it to AWS EC2 with a production-style Nginx reverse proxy and SSL/TLS encryption.

---

### 🚀 Implementation Strategy

This project demonstrates end-to-end container workflow — from local development to a live, secured production deployment:

1. **Containerization:** Built a custom Docker image for the Node.js + Express API using a `Dockerfile`.
2. **Multi-Container Orchestration:** Used Docker Compose to run the app and a MongoDB database as separate, networked containers.
3. **Image Distribution:** Pushed the built image to Docker Hub as a versioned, publicly pullable artifact.
4. **Cloud Deployment:** Pulled the image directly on an AWS EC2 instance and ran it via Docker Compose — no manual dependency installation needed on the server.
5. **Reverse Proxy & Encryption:** Configured Nginx to proxy traffic to the containerized app, then issued a free SSL certificate via Certbot for HTTPS access.
6. **Security Decision:** MongoDB's port is intentionally **not exposed publicly** — it's only reachable within the internal Docker network, reducing the attack surface.

---

### 📊 Visual Evidence & Documentation

#### 1. Local Development — App Running
Verified the containerized app locally before deploying anywhere.
<img width="1920" height="1080" alt="Local homepage" src="https://github.com/user-attachments/assets/replace-with-local-homepage" />

#### 2. Local — Multi-Container Status
Both `app` and `mongo` containers running via `docker compose up`.
<img width="1261" height="373" alt="Local docker ps" src="https://github.com/user-attachments/assets/replace-with-local-docker-ps" />

#### 3. API Verification (POST/GET with Database Persistence)
Confirmed the app correctly writes to and reads from MongoDB.
<img width="1914" height="843" alt="Local API test" src="https://github.com/user-attachments/assets/replace-with-local-api-test" />

#### 4. Docker Hub — Published Image
Image pushed and publicly available for pulling on any host.
<img width="1917" height="931" alt="Docker Hub repository" src="https://github.com/user-attachments/assets/replace-with-dockerhub" />

#### 5. Production Deployment — EC2 Container Status
Same image pulled and running on AWS EC2, orchestrated via Docker Compose.
<img width="1896" height="376" alt="EC2 docker ps" src="https://github.com/user-attachments/assets/replace-with-ec2-docker-ps" />

Note the MongoDB container has no published port — it's isolated inside the Docker network and reachable only by the app container.

#### 6. Live Production Environment — HTTPS Secured
The application accessible over a custom domain with a valid SSL certificate.
<img width="1920" height="1029" alt="HTTPS live" src="https://github.com/user-attachments/assets/replace-with-https-live" />

---

### 🧰 Tech Stack & Tools
* **Containerization:** Docker, Docker Compose
* **Application:** Node.js, Express, Mongoose
* **Database:** MongoDB 7
* **Infrastructure:** AWS EC2 (Ubuntu)
* **Web Server:** Nginx (Reverse Proxy)
* **Security:** Let's Encrypt SSL, Certbot, network-isolated database
* **Registry:** Docker Hub

---

### 📁 Project Structure
* **/app.js** — Express API source code (Notes CRUD with MongoDB).
* **/Dockerfile** — Instructions to build the app image.
* **/docker-compose.yml** — Local multi-container orchestration (app + MongoDB).
* **/docs** — [Step-by-Step Deployment Guide](docs/deployment-guide.md) covering local setup, Docker Hub push, and EC2 deployment with Nginx + SSL.

---

### ⚠️ Troubleshooting & Lessons Learned
1. **`docker-compose` (v1) vs `docker compose` (v2):** The legacy Python-based `docker-compose` CLI threw a `URLSchemeUnknown` error due to a `requests`/`urllib3` version conflict. Resolved by switching to the Docker Compose v2 plugin (`docker compose`, no hyphen).
2. **Push tag mismatch:** Initially pushed without specifying a tag, causing Docker to look for a non-existent `:latest` tag. Fixed by explicitly tagging and pushing the same version (`:v1`) consistently across build and push commands.
3. **Docker permission denied on EC2:** The `ubuntu` user wasn't part of the `docker` group, causing a permission error on `docker ps`. Resolved by adding the user to the `docker` group (`usermod -aG docker $USER`) and starting a fresh session.
