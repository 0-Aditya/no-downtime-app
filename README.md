Updated version: https://github.com/0-Aditya/blue-green-deployment.git
This one doesn't give zero downtime, instead it just provides automatic rollback. The updated version has zero downtime feature.

Zero-Downtime Deployment with Automated Rollback
Docker Compose + Nginx

This project demonstrates a zero-downtime blue-green deployment strategy using Docker Compose and Nginx, with an additional automated rollback mechanism based on application health checks.

Two versions of a UI-based web application run in parallel. Nginx controls traffic routing, and a monitoring script automatically switches traffic back to the stable version if the new version fails — without manual rollback commands.

Problem Statement

In traditional deployments, the existing application is stopped before deploying a new version, causing downtime and requiring manual rollback if the deployment fails.

This project solves that problem by:

Running multiple application versions simultaneously

Switching traffic without stopping services

Automatically reverting to a stable version when failures occur

Technologies Used

Docker

Docker Compose

Nginx (Reverse Proxy)

Bash (Health Check Automation)

HTML/CSS (Basic UI)

Architecture Overview

User → Nginx → Application (v1 or v2)

Users interact only with Nginx

Both application versions run at the same time

Nginx decides which version receives traffic

Project Structure

no-downtime-app
├── docker-compose.yml
├── nginx
│ ├── nginx_v1.conf
│ ├── nginx_v2.conf
│ └── active.conf
├── app_v1
│ ├── Dockerfile
│ └── index.html
├── app_v2
│ ├── Dockerfile
│ └── index.html
└── scripts
└── health_check.sh

How It Works
Blue-Green Deployment

Both app_v1 and app_v2 are running

Nginx always reads active.conf

Switching versions is done by updating active.conf and reloading Nginx

Nginx reloads without restarting containers, ensuring zero downtime

Automated Rollback

A health-check script periodically checks the website

If the HTTP response is not 200, rollback is triggered

Nginx traffic is automatically switched back to app_v1

No manual rollback commands are required

Running the Project

Start all services:

docker-compose up -d --build

Access the application:

http://localhost

Manual Traffic Switch (Zero Downtime)

Deploy v2:

cp nginx/nginx_v2.conf nginx/active.conf
docker exec nginx_proxy nginx -s reload

Rollback to v1:

cp nginx/nginx_v1.conf nginx/active.conf
docker exec nginx_proxy nginx -s reload

Automated Rollback Setup

Start the health-check script in the background:

./scripts/health_check.sh &

The script:

Runs periodically

Monitors application health

Automatically switches traffic back to v1 on failure

Reloads Nginx without restarting containers

Failure Testing

To simulate a failure:

docker stop app_v2

Expected behavior:

Nginx returns a failure response

Health-check script detects the issue

Traffic automatically reverts to app_v1

No manual rollback commands are executed

Users experience no downtime

Key Features

Zero-downtime deployments

Blue-green deployment strategy

Reverse proxy-based traffic control

Automated health monitoring

Automatic rollback on failure

Instant recovery without container restarts

Conclusion

This project demonstrates how Docker Compose and Nginx can be used to implement safe, zero-downtime deployments with automated rollback, without relying on complex orchestration platforms.

It reflects real-world DevOps principles such as traffic isolation, failure detection, and automation-driven recovery.

Future Improvements

Containerize the health-check service

Add alerting or notifications

Implement gradual traffic shifting

Integrate with CI/CD pipelines
