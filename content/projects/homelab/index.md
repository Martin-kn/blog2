---
layout: single
title: HomeLab — Self-Hosted Infrastructure & Security Lab
featured_image: '/assets/images/homelab/lab2.png'
image: '/homelab/lab2.png'
classes: wide
header:
  teaser: /assets/images/homelab/lab2.png
  teaser_home_page: true
  icon: /assets/images/homelab/server.ico
categories:
  - server
tags: ["linux","homelab"]
---

Stack
```
Hypervisor: Proxmox VE
Firewall/Router: OPNsense
IaC: Ansible, Terraform
CI/CD: GitLab CI + local runner
Monitoring: Grafana, InfluxDB2, Zabbix
Security: Nessus, Trivy, Nmap
Networking: VLANs, internal DNS, Tailscale, Nginx Proxy Manager
Auth: Tinyauth (SSO, proxy-level authentication)
```

Production server & homelab running on Proxmox, providing internet access and services to both VMs and physical devices on the network. Used for testing new technologies, practicing IaC, CI/CD pipelines, and security tooling in a real environment.

![Lab!](/images/homelab/lab2.png)

### Project diagram 
![Lab!](/images/homelab2025/homelab2025-4.png)

- NIC1 is passed through directly to the OPNsense VM, which handles all routing and acts as WAN gateway for both VMs and physical devices.

- Trade-off: virtualizing OPNsense optimizes resources and simplifies backups but means full connectivity depends on Proxmox uptime. While this is acceptable in a homelab context, production environments typically run firewalls and routers on dedicated hardware or a separate virtualization layer to avoid this single point of failure.

### Uptime
![Lab!](/images/homelab2025/mordor.png)
The gap reflects a physical network incident — a cable short that caused an unplanned outage. Outside of that event, the server has maintained consistent uptime.

## Infrastructure & Virtualization
```
  ├── Proxmox server
  │   ├── Monitoring
  │   ├── GitlabCI
  │   ├── NPM
  │   ├── IAM
  │   ├── DockerVM
  │   ├── Nessus
  │   ├── OPNsense
  │   ├── NAS
  │   ├── Testing VM1
  │   ├── Testing VM2
  │   ├── ...
  │   ├── ...
  ├── Proxmox node2
  │   ├── ...
  │   ├── ...
  ```

- Configured Tailscale for remote access with ACLs to restrict access per user.
- Standardized VMs and LXC containers on Debian for consistency and easier maintenance — avoiding multiple distros without a clear justification.
- Removed unused VMs and services to reduce complexity and overhead.
- Maintaining an internal wiki for centralized documentation of infrastructure and technologies.
- Homepage dashboard for unified access to internal services.
![Lab!](/images/homelab2025/homepage.png)

## Networking & Security 

### Nginx Proxy manager
Configured as reverse proxy, routing requests to the correct VM/port and managing TLS/SSL certificates for internal services.

### Firewall/Router

OPNsense running as a VM in Proxmox with NIC passthrough, handling routing for both VMs and physical devices. Physical switch configured with VLANs for network segmentation.

### Local DNS
Internal DNS for local service resolution, with blocklists to filter ads, tracking, and malicious domains at the DNS level.

### Authentication & Access Control 
Centralized authentication with SSO and MFA for internal services (Tinyauth), integrated with Nginx Proxy Manager for authentication across all proxied services.

### Security & Vulnerability Analysis
- Deployed Nessus in 2024 for vulnerability scanning across VMs and physical hosts, using scan results to drive remediation.
- Additional scanning with Trivy (container SAST) and hardening scripts for Linux systems.

## CI/CD & IaC

### CI/CD
GitLab CI with local runners deployed on a dedicated VM. Pipelines handle Docker image builds, pushes to a private registry, SAST scanning with Trivy, and deployments to Proxmox.  GitHub Actions used for GitHub-hosted repositories.

### IaC - Infra as a Code

#### Ansible
Used for configuration management across all servers — provisioning, package installation, updates, and running hardening scripts and security scans. Significantly reduced manual administration overhead.

#### Terraform
Used for infrastructure provisioning on Proxmox — defining and managing VMs as code.

## Monitoring
- InfluxDB2 stores metrics from Proxmox and forwards them to Grafana for visualization.
- Grafana as central monitoring hub: dashboards for VMs, Docker containers, LXC, and alerting on errors and anomalies.
- Zabbix for OS monitoring across VMs and containers — services, processes, and system-level alerts.

![Lab!](/images/homelab2025/grafana-server-2.png)

![Lab!](/images/homelab2025/grafana-server-3.png)
![Lab!](/images/homelab2025/grafana-server-4.png)
![Lab!](/images/homelab2025/LXC-grafana.png)

## Services
### NAS

Debian VM with SMB configured as NAS. Media containers mount volumes directly from NAS for centralized file management.

### Multimedia
(Copyright-free content)

Self-hosted media stack: Jellyfin for video streaming and Navidrome for music, both backed by NAS storage. Open-source alternatives to Plex.
![Lab!](/images/homelab2025/tech2.png)

### AI - Ollama

Running DeepSeek locally via Ollama. Currently evaluating resource allocation to move it to a dedicated VM and expose it as a network service.

## IoT / Future plans
In 2021 I built a Raspberry Pi setup with a relay to control devices and lights via Python script.
<!-- ![Relay!](/images/homelab/relay6.gif) -->

<img src="/images/homelab/relay6.gif" width="400" height="auto">

Currently developing a web dashboard to monitor and store metrics from my greenhouse, connected to IoT sensors that feed data to the server for visualization and tracking.

### Upcoming Projects
Planning to add a VM to manage IoT devices over the Zigbee protocol, which is more energy-efficient — particularly relevant for battery-powered devices.

#### Irrigation System - IOT

I am planning an automated irrigation system for my vegetable garden/fruit trees that is controlled by an app and returns information.
Drip irrigation hardware already sourced. Currently working on implementing the automated valve control system and companion app.

Here is a picture of my peaches:
![Proxmox!](/images/homelab2025/frutal.jpeg)

I also have cherry tomato plants, as well as leafy greens, including some Asian varieties.

<!-- ![cherry!](/images/homelab2025/cherry.png) -->


## Lessons Learned

Running this lab since 2021 has meant dealing with real operational challenges — power outages, dependency management, security incidents, and evolving the architecture as requirements grew. The goal has always been to mirror production practices as closely as possible in a personal environment.
