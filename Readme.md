# Docker CI/CD Lab Project

This project demonstrates a simple full-stack application (Express backend + React frontend) deployed using Docker and GitHub Actions. It includes:

- Express.js API
- React.js frontend served with Nginx
- Docker + Docker Compose setup
- CI/CD pipeline with GitHub Actions
- Automated deployment to AWS EC2 Ubuntu instance

---

## 🌐 Live Stack Overview

| Service   | Port   | Description              |
|-----------|--------|--------------------------|
| Frontend  | 3000   | React served via Nginx   |
| Backend   | 5000   | Express REST API         |

---

## 🚀 Quickstart for Beginners

### 1. **Fork & Clone this Repo**
```bash
git clone https://github.com/X5851X/docker_ci_cd_lab.git
cd docker_ci_cd_lab
```

### 2. **Set Up Environment Files**

#### `backend/.env`
```
PORT=5000
```

#### `frontend/.env`
```
PORT=3000
REACT_APP_BACKEND_URL=http://localhost:5000
```

### 3. **Build and Run Locally (Docker Compose)**
```bash
docker-compose up --build
```
Visit:
- http://localhost:3000 (Frontend)
- http://localhost:5000 (Backend)

---

## ☁️ Deploy to AWS EC2 with GitHub Actions

### Prerequisites:
- Docker Hub account
- AWS EC2 (Free Tier Ubuntu 22.04)
- Your EC2 must have Docker installed and ports 22, 80, 3000, 5000 open

### 1. **Set GitHub Secrets**
Go to **Repo → Settings → Secrets → Actions**, and add:

| Secret Key        | Value                          |
|-------------------|--------------------------------|
| `DOCKER_USERNAME` | Your Docker Hub username       |
| `DOCKER_PASSWORD` | Your Docker Hub password       |
| `SSH_HOST`        | EC2 public IP (e.g. `1.2.3.4`) |
| `SSH_USER`        | `ubuntu`                       |
| `SSH_PRIVATE_KEY` | contents of your `.pem` file   |

### 2. **docker.yml Configuration Explained**
In `.github/workflows/docker.yml`, we build and push images like this:
```yaml
- name: Build and push backend
  uses: docker/build-push-action@v4
  with:
    context: ./backend
    push: true
    tags: [your_docker_hub]/backend:latest

- name: Build and push frontend
  uses: docker/build-push-action@v4
  with:
    context: ./frontend
    push: true
    tags: [your_docker_hub]/frontend:latest
```
> Replace `[your_docker_hub]` with your own Docker Hub username if you fork the repo.

This will create two images and push them to Docker Hub:
- `[your_docker_hub]/backend:latest`
- `[your_docker_hub]/frontend:latest`

### 3. **Push to `main` Branch**
```bash
git add .
git commit -m "CI/CD ready"
git push origin main
```

GitHub will:
- Build Docker images
- Push to Docker Hub
- SSH into EC2
- Pull and run the containers

### 4. **On AWS EC2: Pull and Run Manually (if needed)**
SSH into your EC2 server:
```bash
ssh -i your-key.pem ubuntu@your-ec2-ip
```
Then run:
```bash
# Pull images (if GitHub Actions doesn't deploy automatically)
docker pull [your_docker_hub]/backend:latest
docker pull [your_docker_hub]/frontend:latest

# Run containers manually
docker run -d --name backend -p 5000:5000 [your_docker_hub]/backend:latest
docker run -d --name frontend -p 3000:80 [your_docker_hub]/frontend:latest
```

### 5. **Access the App**
Open in browser:
- http://your-ec2-ip:3000 (Frontend)
- http://your-ec2-ip:5000/health (Backend Health)

---

## 🔍 Healthcheck Configuration

### Express Backend:
```js
app.get('/health', (req, res) => res.send('OK'));
```
In `backend/Dockerfile`:
```Dockerfile
RUN apk add --no-cache curl
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit 1
```

### React Frontend:
Create `frontend/public/health.html` with `OK` text.
In `frontend/Dockerfile`:
```Dockerfile
RUN apk add --no-cache curl
HEALTHCHECK CMD curl --fail http://localhost/health.html || exit 1
```

---

## 📂 Project Structure
```
project-root/
├── backend/
│   ├── index.js
│   ├── package.json
│   ├── Dockerfile
│   └── .env
├── frontend/
│   ├── src/
│   │   └── App.js
│   ├── Dockerfile
│   └── .env
├── docker-compose.yml
├── .github/workflows/
│   ├── docker.yml
│   └── deploy.yml
└── README.md
```

---

## 🙌 Credits
Created by [[your_docker_hub]](https://github.com/[your_docker_hub]) as a DevOps CI/CD lab project.

Feel free to fork and try it yourself!