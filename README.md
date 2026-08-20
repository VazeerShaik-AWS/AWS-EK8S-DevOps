# Go Web App — Cloud-Native DevOps on AWS EKS

Production-style application delivery on Amazon EKS: containerize, publish, package with Helm, and expose traffic through NGINX Ingress and an AWS Load Balancer, with GitHub Actions CI and Argo CD GitOps.

---

## Overview

This project deploys a Go web application using a cloud-native delivery path:

* GitHub as the source of truth
* GitHub Actions for build, test, lint, and image publish
* Docker Hub as the container registry
* Helm for Kubernetes packaging
* Argo CD for GitOps on Amazon EKS
* NGINX Ingress and AWS Load Balancer for external access

A single repository carries the application, CI workflow, Kubernetes manifests, and Helm chart.

---

## Architecture

GitHub Repository
→ GitHub Actions (CI)
→ Docker Hub
→ Helm Chart
→ Argo CD (GitOps)
→ Amazon EKS
→ Deployment → Service → NGINX Ingress
→ AWS Load Balancer
→ Users

---

## CI/CD Pipeline

On every push to the main branch:

1. Source is checked out and built in Go
2. Tests and golangci-lint run as quality gates
3. Docker image is built and pushed to Docker Hub
4. Helm chart image tag is updated in Git
5. Argo CD reconciles the cluster to the new tag

---

## Security Design

* CI credentials live in GitHub Secrets, not in source
* Docker Hub and GitHub tokens are injected at pipeline runtime
* AWS access follows IAM least privilege
* Application is not exposed through Pod IPs; traffic enters via Ingress and the load balancer

---

## Platform

### Go Application

Lightweight HTTP service serving `/home` and `/about` on port `8080`. This is the only application code; the rest of the repo exists to ship and run it.

### Docker

Multi-stage Distroless image so the binary runs the same on a laptop and on EKS. Published as `techonline077/web-app`.

### GitHub Actions

CI quality gate: build, test, lint, then publish. Connects Git commits to a versioned image and an updated Helm tag.

### Docker Hub

Registry the cluster pulls from. Kubernetes never builds the app; it only runs the tagged image CI produced.

### Helm

Templates Deployment, Service, and Ingress. CI writes `image.tag` so the next GitOps sync deploys the image that just shipped.

### Argo CD

GitOps control plane on EKS. Cluster state follows the Helm chart in Git.

### Amazon EKS

Managed Kubernetes in Asia Pacific (Mumbai). Hosts the workload, Service, and Ingress controller.

### NGINX Ingress and AWS Load Balancer

Host-based routing (`web-app.local`) from the internet to the Service, then to Pods.

---

## Tech Stack

Cloud: AWS (EKS, EC2, VPC, IAM, Load Balancer)
Containers: Docker, Docker Hub, Kubernetes, Helm, NGINX Ingress
CI/CD: GitHub Actions, Argo CD
Application: Go, golangci-lint

---

## Project Structure

* `main.go` — HTTP server
* `static/` — `/home` and `/about` pages
* `Dockerfile` — multi-stage Distroless image
* `.github/workflows/ci.yaml` — CI pipeline
* `k8s/manifests/` — Deployment, Service, Ingress
* `helm/web-app-chart/` — Helm packaging

---

## Key Highlights

* End-to-end path from Git push to traffic on EKS
* Automated build, test, lint, and image publish
* Helm tag updates wired into GitOps
* Ingress and AWS Load Balancer for external access
* Secrets kept out of source control

---

## Contact

Email: [vazeershaik.aws@gmail.com]
GitHub: https://github.com/VazeerShaik-AWS
Portfolio: https://www.vazeershaik.in

---

## Focus

Building production-grade cloud-native delivery: CI, containers, GitOps, and secure Kubernetes exposure on AWS.
