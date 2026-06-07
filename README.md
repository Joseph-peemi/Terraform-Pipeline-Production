# Terraform Production Pipeline - Java Application IaC

Production-grade pipeline combining **Terraform Infrastructure as Code** with a full CI/CD workflow for a Java (Maven) application on AWS.

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-%235835CC.svg?style=for-the-badge&logo=terraform&logoColor=white)
![Java](https://img.shields.io/badge/Java-17-blue?style=for-the-badge&logo=openjdk&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

## Overview

This repository demonstrates an **end-to-end production pipeline** for a Java application:

- **Infrastructure**: Provisioned using Terraform (VPC, EC2, security groups, etc.)
- **Application**: Multi-module Maven Java project (server + webapp)
- **CI/CD**: Jenkins pipeline for building, testing, Dockerizing, and deploying
- **Containerization**: Optimized Dockerfile for the Java application

## Project Structure
.
├── server/                  # Backend module

├── webapp/                  # Web frontend module

├── .vscode/                 # VS Code settings

├── Dockerfile               # Multi-stage Docker build

├── Jenkinsfile              # Complete CI/CD pipeline

├── pom.xml                  # Root Maven POM
└── README.md


## Features

- **Infrastructure as Code** with Terraform for AWS resources
- **Multi-module Maven** Java project
- **Automated Jenkins Pipeline** (build → test → SonarQube → Docker → deploy)
- **Optimized Docker** multi-stage builds
- Production-ready configuration (health checks, resource management)

## Tech Stack

| Layer              | Technology                          |
|--------------------|-------------------------------------|
| Application        | Java 17 + Maven                     |
| Infrastructure     | Terraform + AWS                     |
| CI/CD              | Jenkins                             |
| Container          | Docker                              |
| Build Tool         | Maven                               |

## Prerequisites

- AWS account with appropriate permissions
- Terraform installed
- Jenkins server with Docker and Maven tools
- AWS credentials configured in Jenkins
- SonarQube (optional but recommended)

## Getting Started

### 1. Infrastructure Provisioning (Terraform)

```bash
cd terraform/          # (if Terraform configs are present)
terraform init
terraform plan
terraform apply

2. Application Build
Bash
mvn clean package

3. Docker Build
Bash
docker build -t your-app:latest
 .
4. Run Jenkins Pipeline
Use the included Jenkinsfile in your Jenkins job. The pipeline typically includes:

Code checkout
Maven build & unit tests
SonarQube analysis
Docker image build & push
Deployment steps

Pipeline Stages (Jenkinsfile)

Workspace cleanup
Git checkout
Maven compile & package
Unit testing
Code quality analysis
Docker image build & push
Post-build notifications
