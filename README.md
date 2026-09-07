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
<img width="1920" height="978" alt="Screenshot from 2026-09-07 15-58-36" src="https://github.com/user-attachments/assets/3ff7f704-d6d3-43a6-91e6-c6bd6b91a1d4" />


#### 2. Local — Multi-Container Status
Both `app` and `mongo` containers running via `docker compose up`.
<img width="1261" height="371" alt="Screenshot from 2026-09-07 15-57-18" src="https://github.com/user-attachments/assets/bd5fda38-9598-481a-8dc7-ed67090368d4" />


#### 3. API Verification (POST/GET with Database Persistence)
Confirmed the app correctly writes to and reads from MongoDB.
<img width="1270" height="697" alt="Screenshot from 2026-09-07 15-56-49" src="https://github.com/user-attachments/assets/0d321b35-0f34-4c6a-9768-e687ef6ddc48" />


#### 4. Docker Hub — Published Image
Image pushed and publicly available for pulling on any host.
<img width="1917" height="928" alt="Screenshot from 2026-09-07 16-25-21" src="https://github.com/user-attachments/assets/b851ae7d-e61a-4522-96aa-1bcb7e1c5732" />


#### 5. Production Deployment — EC2 Container Status
Same image pulled and running on AWS EC2, orchestrated via Docker Compose.
<img width="1896" height="368" alt="Screenshot from 2026-09-07 16-24-52" src="https://github.com/user-attachments/assets/034a536d-c2e0-4b38-a416-3a43db4f5ac5" />


Note the MongoDB container has no published port — it's isolated inside the Docker network and reachable only by the app container.

#### 6. Live Production Environment — HTTPS Secured
The application accessible over a custom domain with a valid SSL certificate.
<img width="1920" height="1018" alt="Screenshot from 2026-09-07 16-26-00" src="https://github.com/user-attachments/assets/b69e7b51-a65c-4a2b-9d03-f6828faba270" />


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

