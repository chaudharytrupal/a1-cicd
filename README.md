# a1-cicd: Continuous Integration and Continuous Deployment

## Overview

This repository contains instructions for setting up a continuous integration and continuous deployment (CI/CD) pipeline which is designed to deploy an application along with a MySQL database on Amazon EC2.

## Prerequisites

- AWS CLI installed
- Docker installed
- Terraform installed
- Amazon ECR repository created
- Access to an Amazon EC2 instance
- Secret variables updated for your AWS account

## Instructions

### 1. Authenticate with Amazon ECR

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "your registry URL"
```

### 2. Create Docker Bridge Network

```bash
docker network create --driver bridge --subnet 199.199.0.0/16 my_network
```

### 3. Set Environment Variables

```bash
export REGISTRY="your registry url"
```

### 4. Run MySQL Database Container

```bash
docker run -d --network my_network --name my_db -e MYSQL_ROOT_PASSWORD=pw $REGISTRY/my_db:v1
```

### 5. Export ENV Vars

```bash
export DBHOST=199.199.0.2
export DBPORT=3306
export DBUSER=root
export DATABASE=employees
export DBPWD=pw
```

### 6. RUN 3 instances of the same webserver image

```bash
docker run -d --network my_network -p 8081:8080 --name blue -e APP_COLOR=blue -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e DBUSER=$DBUSER -e DBPWD=$DBPWD $REGISTRY/my_app:v1
```


```bash
docker run -d --network my_network -p 8082:8080 --name pink -e APP_COLOR=pink -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e DBUSER=$DBUSER -e DBPWD=$DBPWD $REGISTRY/my_app:v1
```


```bash
docker run -d --network my_network -p 8083:8080 --name lime -e APP_COLOR=lime -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e DBUSER=$DBUSER -e DBPWD=$DBPWD $REGISTRY/my_app:v1
```

