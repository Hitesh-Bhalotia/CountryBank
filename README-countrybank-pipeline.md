# Country Bank Application - Jenkins Pipeline

This Jenkins pipeline automates the CI/CD process for the Country Bank application with comprehensive security scanning and containerized deployment.

## 📄 Project Attribution

**Important Note**: This project is a fork of the original Country Bank application created by [nikitap492](https://github.com/nikitap492/CountryBank).

- **Original Repository**: https://github.com/nikitap492/CountryBank
- **Fork Repository**: https://github.com/Hitesh-Bhalotia/CountryBank.git
- **My Contribution**: The Jenkins pipeline configuration and CI/CD automation setup documented in this README

All credit for the Country Bank application code (frontend, backend, and core functionality) goes to the original author. This documentation focuses specifically on the Jenkins pipeline implementation and DevOps automation added to the forked repository.

## 🏗️ Pipeline Overview

This streamlined declarative Jenkins pipeline performs the following operations:
- Source code checkout from GitHub
- Comprehensive security vulnerability scanning (OWASP & Trivy)
- Containerized deployment using Docker Compose

## 📋 Prerequisites

### Jenkins Requirements
- Jenkins server with pipeline plugin support
- Required Jenkins plugins:
  - Git Plugin
  - OWASP Dependency-Check Plugin
  - Pipeline Plugin
  - Docker Plugin
  - Eclipse Temurin installer

### Tool Configurations
Configure the following tools in Jenkins Global Tool Configuration:

#### JDK Configuration
- **Name**: `jdk`
- **Type**: JDK installation
- **Installation**: Eclipse Temurin installer plugin (automatic)
- **Version**: Latest LTS version recommended

#### Gradle Configuration
- **Name**: `gradle`
- **Type**: Gradle installation
- **Installation**: Automatic installation
- **Version**: v1.5.4 (automatically detected from build.gradle file)

#### Docker Configuration
- **Name**: `docker`
- **Type**: Docker installation
- **Installation**: Automatic download and installation
- **Integration**: Ensures Docker commands are available in pipeline

#### OWASP Dependency Check
- **Name**: `DC`
- **Type**: Dependency-Check installation
- **Installation**: Automatic installer or manual installation

### System Dependencies
Ensure the following tools are installed on Jenkins agents:
- **Trivy**: Container and filesystem vulnerability scanner
- **Docker**: For containerized deployment
- **Docker Compose**: For multi-container orchestration
- **Git**: For source code management

## 🚀 Pipeline Stages

### 1. Git Checkout
```groovy
git 'https://github.com/Hitesh-Bhalotia/CountryBank.git'
```
- **Purpose**: Clones the source code from the repository
- **Repository**: `https://github.com/Hitesh-Bhalotia/CountryBank.git`
- **Branch**: Default branch (typically `main` or `master`)
- **Method**: Simple Git checkout without branch specification

### 2. OWASP Dependency Check
```groovy
dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
```
- **Purpose**: Scans entire project for known vulnerabilities in dependencies
- **Tool**: OWASP Dependency Check
- **Scope**: Current directory (`./`) - scans all files and dependencies
- **Output**: Generates `dependency-check-report.xml`
- **Integration**: Automatically publishes results to Jenkins dashboard
- **Coverage**: All dependency files (package.json, requirements.txt, pom.xml, etc.)

### 3. Trivy Filesystem Scan
```groovy
sh "trivy fs ."
```
- **Purpose**: Performs comprehensive filesystem vulnerability scanning
- **Tool**: Trivy security scanner
- **Scope**: Entire project directory (`.`)
- **Detects**: 
  - CVE vulnerabilities
  - Security misconfigurations
  - Exposed secrets and credentials
  - License compliance issues
  - Infrastructure as Code (IaC) issues

### 4. Build & Deploy
```groovy
sh "docker-compose up -d"
```
- **Purpose**: Builds and deploys the application using Docker Compose
- **Command**: Runs in detached mode (`-d`) for background execution
- **Requirement**: `docker-compose.yml` must exist in project root
- **Process**: 
  - Builds Docker images (if Dockerfile present)
  - Creates and starts containers
  - Sets up networking between services



## 📊 Security Scanning Reports

### OWASP Dependency Check
- **Report Location**: Workspace root as `dependency-check-report.xml`
- **Jenkins Integration**: Automatically published with trend graphs
- **Access**: Build results → Dependency-Check Results
- **Information**: 
  - Known vulnerabilities (CVE)
  - CVSS scores
  - Affected components
  - Remediation suggestions

### Trivy Scan Results
- **Output**: Jenkins console logs
- **Coverage**: 
  - Operating system packages
  - Language-specific packages (npm, pip, gem, etc.)
  - Application dependencies
  - Container images (if scanning images)
- **Integration**: Consider adding `--format json` and archiving results

---

**Note**: This pipeline is optimized for the Country Bank application. Ensure your project structure and Docker configuration match the requirements outlined in this documentation.
