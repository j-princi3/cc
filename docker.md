---

# 🧪 **Experiment 2: Deploy a Front-End Application Using Docker**

---

## ✅ **Why This Experiment?**

Deploying a front-end application with Docker on AWS helps you understand key DevOps and cloud concepts:

* **Containerization**: Packages the app with all dependencies
* **Portability**: Run the app anywhere Docker is installed
* **Automation**: Simplifies deployment and scaling
* **Cloud Infrastructure**: Practice real-world deployment on EC2

By the end of this lab, you'll know how to:

* Set up Docker on a cloud server
* Build and run containerized web applications
* Expose a Docker container publicly using EC2 security groups

---

## ✅ **Pre-Requisites**

* AWS EC2 instance (Ubuntu preferred)
* SSH access with key pair
* Git installed (usually pre-installed on Ubuntu)

---

# 🧩 **Task 1: Install Docker on an EC2 Instance**

### 🔷 **Why?**

Docker is the container engine that allows us to build and run applications in isolated environments.

### 🔧 **Steps to Execute**

1. **SSH into your EC2 instance**

```bash
ssh -i "your-key.pem" ubuntu@<PublicIP>
```

2. **Update system and install Docker**

```bash
sudo apt update
sudo apt install docker.io -y
```

3. **Start Docker service and enable on boot**

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

4. **(Optional) Add user to Docker group**

```bash
sudo usermod -aG docker ubuntu
newgrp docker
```

5. **Verify Docker installation**

```bash
docker --version
```

✅ **Docker is now installed and ready to use!**

---

# 🧩 **Task 2: Clone the Front-End App, Write Dockerfile, Build Image**

### 🔷 **Why?**

To containerize the application, we need to:

* Get the app source code
* Write instructions (Dockerfile) for building the container image

### 🔧 **Steps to Execute**

1. **Clone the repository**

```bash
git clone https://tinyurl.com/demokmit3
cd demokmit3
```

2. **Create a `Dockerfile` in the project directory**

```bash
nano Dockerfile
```

3. **Paste the following content into the Dockerfile:**

```Dockerfile
# Use an official Node.js base image
FROM node:18

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy source code
COPY . .

# Build the application (for React/Vue/Angular)
RUN npm run build

# Install a static server (like serve)
RUN npm install -g serve

# Expose port 3000 or as needed
EXPOSE 3000

# Command to run the app
CMD ["serve", "-s", "build", "-l", "3000"]
```

4. **Build the Docker image**

```bash
docker build -t frontend-app .
```

✅ You now have a Docker image of your front-end app!

---

# 🧩 **Task 3: Create a Docker Container for the Front-End App**

### 🔷 **Why?**

Running the image inside a container starts the actual application that will be accessible via the web.

### 🔧 **Steps to Execute**

1. **Run the container and map port 3000**

```bash
docker run -d -p 3000:3000 --name frontend-container frontend-app
```

2. **Check running containers**

```bash
docker ps
```

✅ Your app is now running in a Docker container on port 3000.

---

# 🧩 **Task 4: Configure Security Groups to Allow Web Traffic**

### 🔷 **Why?**

To access the application from your browser, you must open the necessary port (e.g., 3000) in the EC2 instance’s security group.

### 🔧 **Steps to Execute**

1. Go to **AWS Console > EC2 > Instances**
2. Select your instance → Click on **Security Group**
3. Click **Edit inbound rules**
4. Add a new rule:

   * **Type**: Custom TCP
   * **Port**: `3000`
   * **Source**: Anywhere (or your IP for security)
5. Save the rule

✅ You have now exposed the Docker app to the internet!

---

# 🌐 **Access the Front-End Application**

Open a browser and navigate to:

```plaintext
http://<EC2-Public-IP>:3000
```

You should see your front-end application running.

---

# 📝 **Summary Table**

| Task   | Description                        | Commands/Tools                  |
| ------ | ---------------------------------- | ------------------------------- |
| Task 1 | Install Docker on EC2              | `apt install docker.io`         |
| Task 2 | Clone app, write Dockerfile, build | `git clone`, `docker build`     |
| Task 3 | Create Docker container            | `docker run -p 3000:3000`       |
| Task 4 | Allow public access to app         | AWS Security Groups (port 3000) |

---

Would you like this packaged as a **PDF lab manual**, **Bash automation script**, or **Docker Compose** setup?
