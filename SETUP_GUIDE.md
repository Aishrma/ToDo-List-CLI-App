# Jenkins & Docker Setup Guide for To-Do List CLI Application

## Complete Step-by-Step Instructions

---

## 📋 Prerequisites

- ✅ Windows OS
- ✅ WSL installed
- ✅ Docker Desktop installed
- ✅ Jenkins for Windows installed

---

## Part 1: Verify Docker Installation

### Step 1: Start Docker Desktop
1. Open Docker Desktop from Start Menu
2. Wait until Docker Engine is running (whale icon in system tray should be stable)
3. Open PowerShell and verify:

```powershell
docker --version
docker ps
```

You should see Docker version and an empty container list (or running containers).

---

## Part 2: Install and Configure Jenkins

### Step 2: Download Jenkins (if not already done)

1. Go to: https://www.jenkins.io/download/
2. Download **Jenkins for Windows** (Generic Java Package `.war` file OR Windows installer)
3. **Recommended**: Download the Windows installer (.msi)

### Step 3: Install Jenkins

**Option A - Using Windows Installer (.msi):**
1. Run the Jenkins installer
2. Follow the installation wizard
3. Default port: 8080
4. Jenkins will install as a Windows service

**Option B - Using WAR file:**
```powershell
java -jar jenkins.war --httpPort=8080
```

### Step 4: Initial Jenkins Setup

1. Open browser and go to: `http://localhost:8080`

2. **Unlock Jenkins:**
   - Find the initial admin password at:
     ```
     C:\Program Files\Jenkins\secrets\initialAdminPassword
     ```
   - Or check the console output where Jenkins started
   - Copy and paste the password

3. **Install Plugins:**
   - Select "Install suggested plugins"
   - Wait for installation to complete

4. **Create Admin User:**
   - Username: (your choice)
   - Password: (your choice)
   - Full Name: (your name)
   - Email: (your email)
   - Click "Save and Continue"

5. **Instance Configuration:**
   - Keep default: `http://localhost:8080/`
   - Click "Save and Finish"
   - Click "Start using Jenkins"

### Step 5: Install Required Jenkins Plugins

1. Go to: **Dashboard → Manage Jenkins → Plugins**
2. Click on **Available plugins** tab
3. Search and install these plugins:
   - ✅ **Docker Pipeline**
   - ✅ **Docker Plugin**
   - ✅ **Git Plugin** (usually pre-installed)
   - ✅ **Pipeline**
   - ✅ **GitHub Integration Plugin**

4. Check the boxes and click **"Install"**
5. Check **"Restart Jenkins when installation is complete"**

---

## Part 3: Configure Docker in Jenkins

### Step 6: Add Docker to Jenkins System Path

1. Go to: **Dashboard → Manage Jenkins → System**
2. Scroll to **Global properties**
3. Check **"Environment variables"**
4. Add new environment variable:
   - **Name:** `PATH+DOCKER`
   - **Value:** `C:\Program Files\Docker\Docker\resources\bin`
5. Click **Save**

---

## Part 4: Set Up Docker Hub

### Step 7: Create Docker Hub Account

1. Go to: https://hub.docker.com/
2. Sign up with your roll number as username (e.g., `21bcs123` or similar)
3. Verify your email
4. Login to Docker Hub

### Step 8: Add Docker Hub Credentials to Jenkins

1. In Jenkins, go to: **Dashboard → Manage Jenkins → Credentials**
2. Click on **(global)** domain
3. Click **Add Credentials**
4. Fill in:
   - **Kind:** Username with password
   - **Scope:** Global
   - **Username:** Your Docker Hub username (your roll number)
   - **Password:** Your Docker Hub password
   - **ID:** `dockerhub-credentials` (IMPORTANT: use this exact ID)
   - **Description:** Docker Hub Credentials
5. Click **Create**

---

## Part 5: Push Code to GitHub

### Step 9: Create GitHub Repository

1. Go to: https://github.com/
2. Click **"New repository"**
3. Repository name: `todo-app-jenkins`
4. Keep it **Public**
5. Click **"Create repository"**

### Step 10: Initialize Git and Push Code

Open PowerShell in your project directory:

```powershell
cd "C:\Users\Aishwarya Sharma\Desktop\College Work\Semester 5\CSE Core\SE Lab\SE_Lab_25-11-25\todo-app"

# Initialize git repository
git init

# Add all files
git add .

# Commit files
git commit -m "Initial commit: To-Do List CLI Application"

# Add remote origin (replace YOUR_USERNAME with your GitHub username)
git remote add origin https://github.com/YOUR_USERNAME/todo-app-jenkins.git

# Push to GitHub
git branch -M main
git push -u origin main
```

---

## Part 6: Create Jenkins Pipeline

### Step 11: Create New Pipeline Job

1. On Jenkins Dashboard, click **"New Item"**
2. Enter name: `todo-app-pipeline`
3. Select **"Pipeline"**
4. Click **OK**

### Step 12: Configure Pipeline

1. **General Section:**
   - Description: `CI/CD Pipeline for To-Do List Application`
   - ✅ Check **"GitHub project"**
   - Project url: `https://github.com/YOUR_USERNAME/todo-app-jenkins/`

2. **Build Triggers:**
   - ✅ Check **"Poll SCM"** (optional, for automatic builds)
   - Schedule: `H/5 * * * *` (checks every 5 minutes)

3. **Pipeline Section:**
   - Definition: **"Pipeline script from SCM"**
   - SCM: **Git**
   - Repository URL: `https://github.com/YOUR_USERNAME/todo-app-jenkins.git`
   - Credentials: Leave as `- none -` (for public repo)
   - Branch: `*/main`
   - Script Path: `Jenkinsfile`

4. Click **Save**

### Step 13: Update Jenkinsfile with Your Roll Number

Before running the pipeline, update the Jenkinsfile:

1. Open: `todo-app/Jenkinsfile`
2. Find line 6: `DOCKERHUB_USERNAME = 'your_roll_number'`
3. Replace `your_roll_number` with your actual Docker Hub username
4. Save the file
5. Commit and push:

```powershell
git add Jenkinsfile
git commit -m "Updated Docker Hub username"
git push
```

---

## Part 7: Run the Pipeline

### Step 14: Build the Pipeline

1. Go to your pipeline: **Dashboard → todo-app-pipeline**
2. Click **"Build Now"** on the left sidebar
3. Watch the build progress in **"Build History"**
4. Click on the build number (e.g., #1) to see details
5. Click **"Console Output"** to see real-time logs

### Step 15: Pipeline Stages

Your pipeline will execute these stages:

1. **Checkout** ✓ - Pulls code from GitHub
2. **Build** ✓ - Verifies Python and lists files
3. **Test** ✓ - Runs unit tests
4. **Build Docker Image** ✓ - Creates Docker image
5. **Push to Docker Hub** ✓ - Pushes image to your Docker Hub
6. **Verify Docker Image** ✓ - Confirms image creation

### Step 16: Monitor the Build

- **Blue balls** 🔵 = Build in progress
- **Green checkmarks** ✅ = Stage successful
- **Red X** ❌ = Stage failed

---

## Part 8: Verify Docker Image

### Step 17: Check Local Docker Images

Open PowerShell:

```powershell
docker images
```

You should see:
```
REPOSITORY                  TAG       IMAGE ID       CREATED         SIZE
your_roll_number/todo-app   latest    xxxxxxxxxxxxx  X minutes ago   XXX MB
your_roll_number/todo-app   1         xxxxxxxxxxxxx  X minutes ago   XXX MB
```

### Step 18: Verify on Docker Hub

1. Login to Docker Hub: https://hub.docker.com/
2. Go to **"Repositories"**
3. You should see: `todo-app`
4. Click on it to see the pushed images with tags

### Step 19: Test the Docker Image

Run your Docker image locally:

```powershell
docker run -it your_roll_number/todo-app:latest
```

The To-Do List application should start in interactive mode.

---

## Part 9: Test Your Application

### Step 20: Manual Testing

Test the application features:

1. Add a task
2. View all tasks
3. Complete a task
4. Delete a task
5. Clear all tasks
6. Exit

---

## 🎯 Summary of What You've Accomplished

✅ Installed and configured Jenkins on Windows
✅ Integrated Docker with Jenkins
✅ Created a Docker Hub account
✅ Pushed code to GitHub
✅ Created an automated Jenkins Pipeline that:
   - Pulls code from GitHub
   - Builds the application
   - Runs unit tests
   - Creates a Docker image
   - Pushes the image to Docker Hub
✅ Verified the Docker image locally and on Docker Hub

---

## 📁 Final Directory Structure

```
todo-app/
├── todo.py              # Main To-Do List application
├── tests/
│   └── test_todo.py     # Unit tests (15 test cases)
├── Dockerfile           # Docker configuration
├── Jenkinsfile          # Jenkins pipeline script
├── README.md            # Project documentation
└── .gitignore          # Git ignore file
```

---

## 🔧 Troubleshooting

### Issue 1: Docker not found in Jenkins
**Solution:** 
- Verify Docker Desktop is running
- Add Docker path to Jenkins environment variables
- Restart Jenkins: `http://localhost:8080/restart`

### Issue 2: Tests failing
**Solution:**
```powershell
# Run tests locally first
cd todo-app
python -m unittest discover -s tests -p "test_*.py" -v
```

### Issue 3: Docker push fails
**Solution:**
- Verify Docker Hub credentials in Jenkins
- Check credential ID is exactly: `dockerhub-credentials`
- Login manually: `docker login`

### Issue 4: GitHub authentication
**Solution:**
- For private repos, add GitHub credentials in Jenkins
- Or use Personal Access Token instead of password

### Issue 5: Permission denied on Docker socket
**Solution:**
- Ensure Docker Desktop is running
- Restart Docker Desktop
- Check Docker settings → Resources → WSL Integration

---

## 📝 Commands Reference

### Docker Commands:
```powershell
# List images
docker images

# List containers
docker ps -a

# Remove image
docker rmi image_name

# Run container
docker run -it image_name

# Stop container
docker stop container_id

# Remove container
docker rm container_id

# Login to Docker Hub
docker login

# Push image
docker push username/image:tag
```

### Jenkins Commands:
```powershell
# Restart Jenkins (in browser)
http://localhost:8080/restart

# View Jenkins home directory
C:\Program Files\Jenkins
```

### Git Commands:
```powershell
# Check status
git status

# Add changes
git add .

# Commit
git commit -m "message"

# Push
git push origin main

# Pull latest
git pull origin main
```

---

## 🎓 Submission Checklist

For your lab assignment, ensure:

- [ ] Jenkins pipeline runs successfully (all stages green)
- [ ] Docker image visible in `docker images` command
- [ ] Docker image pushed to Docker Hub under your roll number
- [ ] Screenshots of:
  - [ ] Jenkins pipeline success
  - [ ] Docker images output
  - [ ] Docker Hub repository
  - [ ] Application running in Docker
- [ ] GitHub repository URL
- [ ] Docker Hub repository URL

---

## 📞 Need Help?

Common commands to check status:

```powershell
# Check Docker
docker --version
docker ps

# Check Jenkins
# Open: http://localhost:8080

# Check Git
git status
git remote -v

# Run app locally
python todo.py

# Run tests
python -m unittest discover -s tests -v
```

---

**Good luck with your lab assignment! 🚀**
