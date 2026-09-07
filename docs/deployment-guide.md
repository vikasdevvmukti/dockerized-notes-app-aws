# 📖 Deployment Guide — Dockerized Notes App

### 🛠 Phase 1: Local Development & Testing

Build and run the app locally using Docker Compose:

```bash
docker compose up --build
```

This builds the `app` image from the Dockerfile and pulls the official `mongo:7` image, then starts both containers networked together.

Verify containers are running:

```bash
docker ps
```

Test the API:

```bash
curl -X POST localhost:3000/notes -H "Content-Type: application/json" -d '{"text":"My first note"}'
curl -X GET localhost:3000/notes
```

---

### 🛠 Phase 2: Push Image to Docker Hub

Log in to Docker Hub:

```bash
docker login
```

Build and tag the image with your Docker Hub username:

```bash
docker build -t your-dockerhub-username/notes-app:v1 .
```

Push the image:

```bash
docker push your-dockerhub-username/notes-app:v1
```

> Note: Only the custom `app` image needs to be pushed. MongoDB uses the official public `mongo:7` image directly from Docker Hub.

---

### 🛠 Phase 3: Deploy on AWS EC2

SSH into the EC2 instance:

```bash
ssh -i your-key.pem -p 2222 ubuntu@your-ec2-ip
```

Install Docker and Docker Compose plugin (if not already installed):

```bash
sudo apt update
sudo apt install docker.io docker-compose-v2 -y
sudo systemctl start docker
sudo systemctl enable docker
```

Add your user to the `docker` group to avoid needing `sudo` for every command:

```bash
sudo usermod -aG docker $USER
```

Log out and back in for the group change to take effect.

Create a project folder and the Compose file:

```bash
mkdir ~/notes-app && cd ~/notes-app
nano docker-compose.yml
```

Paste the following (pulls the pre-built image instead of building locally):

```yaml
services:
  app:
    image: your-dockerhub-username/notes-app:v1
    ports:
      - "3000:3000"
    environment:
      - MONGO_URI=mongodb://mongo:27017/notesapp
    depends_on:
      - mongo
    restart: unless-stopped

  mongo:
    image: mongo:7
    volumes:
      - mongo-data:/data/db
    restart: unless-stopped

volumes:
  mongo-data:
```

Start the containers:

```bash
docker compose up -d
```

Verify both containers are running:

```bash
docker ps
```

---

### 🛠 Phase 4: Nginx Reverse Proxy + SSL

Create an Nginx server block for the app:

```bash
sudo nano /etc/nginx/sites-available/notes-app
```

Paste:

```nginx
server {
    listen 80;
    server_name your-subdomain.duckdns.org;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable the config and reload Nginx:

```bash
sudo ln -s /etc/nginx/sites-available/notes-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Issue an SSL certificate with Certbot:

```bash
sudo certbot --nginx -d your-subdomain.duckdns.org
```

Verify HTTPS is working by visiting `https://your-subdomain.duckdns.org` — the padlock icon should confirm a valid certificate.
