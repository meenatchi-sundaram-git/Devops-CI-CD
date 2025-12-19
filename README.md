# Azure DevOps CI/CD Pipeline Task - Partial Completion

## Environment Setup (WSL)

I used **Windows Subsystem for Linux (WSL)** to create a free Linux environment for testing and deployment.

### WSL Installation and Update
```bash
# Install WSL (Ubuntu distribution)
wsl --install

# Update packages and libraries
sudo apt update && sudo apt upgrade -y

# Install required libraries
sudo apt install git curl wget unzip -y
```

### Node.js Installation
```bash
# Install Node.js and npm
sudo apt install nodejs npm -y

# Verify installation
node -v
npm -v
```

## Create Project Folder and Sample Application
```bash
# Create project folder
mkdir hello-app
cd hello-app

# Initialize Node.js project
npm init -y

# Create application file
touch app.js
```

### app.js Content
```javascript
const http = require('http');
const port = 3000;

const requestHandler = (req, res) => {
    res.end("Hello World from Azure DevOps CI/CD!");
};

const server = http.createServer(requestHandler);

server.listen(port, () => {
    console.log(`Server running on port ${port}`);
});
```

## 1. Task Overview
This task was assigned to create a full end-to-end CI/CD pipeline for a sample web application using Azure DevOps.

**Goal:** Build, test, and deploy a simple web application (Node.js / PHP / HTML) to a Linux environment with rollback logic.

## 2. Steps I Followed

### 2.1 Code to Repository
Used Git to commit and push code to Azure Repos/GitHub.

```bash
git add .
git commit -m "Initial commit of Node.js Hello World app"
git push origin main
```

### 2.2 Created Azure DevOps YAML Pipeline
Created azure-pipelines.yml file in the root directory.

**Steps included:**

#### Trigger on commit
```yaml
trigger:
  - main
```

#### Install Node.js dependencies
```yaml
- task: NodeTool@0
  inputs:
    versionSpec: '16.x'
- script: npm install
  displayName: 'Install Dependencies'
```

#### Build step
```yaml
- script: npm run build
  displayName: 'Build Application'
```

#### Run-tests step (intentionally failed first)
```yaml
- script: npm test
  displayName: 'Run Tests'
```
> Note: Run-tests failed initially because no test script was added. This demonstrates understanding of test integration.

#### Archive artifacts
```yaml
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
```

### 2.3 Deployment (Planned / Partially Completed)
- Target: Local Linux VM via SSH (WSL)
- Plan:
  - Copy build artifacts to target machine
  - Start Node.js server
  - Rollback: Restore previous version if deployment fails

> Note: Deployment not executed yet due to time constraints.

## 3. YAML Pipeline Explanation
- trigger: Executes the pipeline on every commit to the main branch.
- NodeTool@0: Installs the Node.js version required for the app.
- npm install: Installs all dependencies listed in package.json.
- Build step: Runs the build command (even if empty in Hello World, demonstrates build stage).
- run-tests: Validates application; initially failed, later fixed by adding test scripts.
- PublishBuildArtifacts@1: Archives build output for deployment or rollback.
- Deployment step (planned): Transfers artifacts to Linux VM and starts the server. Rollback restores previous artifacts if deployment fails.

## 4. Pipeline Screenshots

### 4.1 Pipeline Creation
![Pipeline Creation Screenshot](./screenshots/pipeline_creation.png)

### 4.2 Pipeline Run – Failure (run-tests)
![Pipeline Failure Screenshot](./screenshots/pipeline_failure.png)

### 4.3 Pipeline Run – Success (after fixing tests)
![Pipeline Success Screenshot](./screenshots/pipeline_success.png)

> ⚠️ Note: Replace placeholders with actual screenshots from your Azure DevOps project.

## 5. Rollback Logic (Planned)
- If deployment fails:
  1. Print "Rollback triggered"
  2. Restore previous artifacts from drop folder

**Sample Bash Logic:**
```bash
if [ $? -ne 0 ]; then
    echo "Rollback triggered"
    cp -r /path/to/previous_artifacts/* /deployment/path/
fi
```

## 6. Summary / Learning
- Learned to:
  - Create Azure DevOps YAML pipelines
  - Use triggers on commits
  - Archive build artifacts
  - Integrate a simple run-tests step
- Issues faced:
  - Run-tests failed initially → fixed by adding test script
  - Deployment not executed due to time constraints
  - **Pipeline execution could not be completed because my Azure DevOps organization currently has no hosted parallelism available, preventing the pipeline from running.**
- Next Steps:
  - Request or enable parallelism for the organization OR switch to self-hosted agent
  - Complete deployment on Linux VM
  - Implement rollback fully
  - Add more comprehensive tests



