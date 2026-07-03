# End-to-End Deployment of a Containerized React + Django Application on AWS

## Project Overview

**Project Name:** Containerized React + Django Web Application Deployment on AWS

**GitHub Repository:** [https://github.com/Shaam1995/react_django_demo_app.git]

## Objective

Deploy a Dockerized React + Django application on AWS using a secure network architecture where:

1. Application containers run on EC2 instances in a Private Subnet
2. Users access the application through an Application Load Balancer (ALB)
3. SSH access to private instances is only through a Bastion Host
4. SQL database runs on the private EC2 instance
5. Infrastructure is monitored using Prometheus and Grafana

## AWS Services Used

- Amazon VPC
  - Public Subnet
  - Private Subnet
- Internet Gateway
- NAT Gateway
- Bastion Host
- EC2 Ubuntu
- Application Load Balancer (ALB)
- Target Group
- Security Groups
- Docker
- Docker Hub
- Git & GitHub
- Prometheus
- Grafana

---

## Deployment Workflow

### Phase 1 – Source Code

Clone the application:

```bash
git clone https://github.com/Shaam1995/react_django_demo_app.git
```

### Phase 2 – Dockerization

**1. Build the Docker image:**

```bash
docker build -t react-django-app .
```

**2. Verify:**

```bash
docker images
```

**3. Run locally:**

```bash
docker run -d -p 8000:8001 react-django-app
```

**4. Test:**

```
http://localhost:8000
```

### Phase 3 – Docker Hub

**Login:**

```bash
docker login
```

**Push:**

```bash
docker push sminathan/python_container:tagname
```

---

## AWS Infrastructure

### VPC

| Attribute | Value |
|-----------|-------|
| CIDR | 10.0.0.0/16 |

### Public Subnet

Contains:
- Bastion Host
- Application Load Balancer
- NAT Gateway

### Private Subnet

Contains:
- Ubuntu EC2
- Docker Container
- SQL Database
- Prometheus
- Grafana

### Bastion Host

**Purpose:** Secure SSH access into private EC2.

**Example:**

```bash
ssh -i web.pem ubuntu@Public-IP
```

Then:

```bash
ssh ubuntu@Private-IP
```

---

## Docker Deployment

1. SSH into private EC2.

2. Clone project:

```bash
git clone https://github.com/Shaam1995/react_django_demo_app.git
```

3. Move inside:

```bash
cd react_django_demo_app
```

4. Build:

```bash
docker build -t react-django-app .
```

5. Run:

```bash
docker run -d -p 8000:8001 react-django-app
```

6. Verify:

```bash
docker ps
```

---

## SQL Database

Install SQL database on the private EC2.

---

## Application Load Balancer

| Attribute | Value |
|-----------|-------|
| Type | Internet-facing |
| Listener | 80 |
| Target Group Port | 8000 |
| Health Check | / |

Register EC2 instance with the target group.

---

## User Flow

```
User → ALB DNS → Private Subnet → Docker Container → Application → SQL DB
```

---

## Monitoring

### Node Exporter

Installed on private EC2.

Collects:
- CPU
- Memory
- Disk
- Network

### Prometheus

Scrapes metrics from:
- Node Exporter

| Attribute | Value |
|-----------|-------|
| Port | 9090 |

### Grafana

Connected to Prometheus.

| Attribute | Value |
|-----------|-------|
| Port | 3000 |

**Dashboard monitors:**
- CPU Usage
- Memory Usage
- Disk Usage
- Docker Container Status
- Network Traffic
- System Load

---

## Security

### Security Groups

**ALB Security Group**

| Direction | Port | Source |
|-----------|------|--------|
| Inbound | 80 | 0.0.0.0/0 |

**Bastion Security Group**

| Direction | Port | Source |
|-----------|------|--------|
| Inbound | 22 | Your Public IP |

**Private EC2 Security Group**

| Direction | Port | Source |
|-----------|------|--------|
| Inbound | 22 | From Bastion SG |
| Inbound | 8000 | From ALB SG |
| Inbound | 9090 | From Grafana SG (or monitoring instance) |
| Inbound | 9100 | From Prometheus SG |
