# Day 03 - Services deployment and remote access

## Objective

Start deploying real services on the server using Docker and make them accessible remotely in a secure way.

---

## What I did

### Docker usage

- Installed and started using Docker to run services in containers
- Understood the difference between:
  - images (templates)
  - containers (running instances)
- Tested containers using:
  - `docker run`
  - `docker ps`
  - `docker logs`
- Learned how to stop and remove containers

---

### First services deployment

#### Portainer

- Deployed Portainer as a Docker container
- Exposed it on port `9000`
- Used it to visualize and manage running containers

What I learned:
- How to map ports (`-p`)
- How to use Docker volumes for persistent data
- How to interact with Docker through a web interface

---

#### Homepage (dashboard)

- Deployed a simple dashboard container
- Exposed it on port `3000`

What I understood:
- How to organize multiple services
- Importance of having a central entry point

---

#### Nginx

- Deployed an Nginx container
- Tried to expose it on port `80`
- Encountered a port conflict

Problem:
- Port 80 was already used by a system Nginx service

Solution:
- Identified the process using `ss -tulpn`
- Stopped and disabled the system Nginx service
- Redeployed the Docker container successfully

What I learned:
- Difference between host services and containers
- How port conflicts happen
- How to troubleshoot service issues

---

### Remote access with Tailscale

- Installed Tailscale on the server
- Connected the server to my private network (tailnet)
- Retrieved the Tailscale IP

Test:
- Accessed my server from another network (4G/5G)

What I learned:
- Difference between:
  - local IP
  - public IP
  - VPN (Tailscale) IP
- Secure remote access without exposing ports to the internet

---

### Vaultwarden deployment (password manager)

- Deployed Vaultwarden using Docker
- Exposed it on port `8081`
- Configured persistent storage using a volume

What I learned:
- Importance of persistent data (`/data`)
- Managing sensitive services
- Using Docker for real-world applications

---

## Issues encountered

### Browser security warning

- Vaultwarden displayed a warning about insecure context
- Cause:
  - Service accessed via HTTP instead of HTTPS
  - Modern browsers require HTTPS for cryptographic features

---

## What I understood

- Containers allow easy and fast deployment of services
- Ports are critical in networking and service exposure
- A service can fail even if correctly installed (port conflicts, etc.)
- Secure access is more important than simply exposing services
- Not all services should be publicly exposed (especially sensitive ones)

---

## Questions

- How to properly implement HTTPS for self-hosted services?
- What is the best way to organize multiple services behind a single entry point?
- How does a reverse proxy actually work in detail?

---

## Next steps

- Implement HTTPS for Vaultwarden
- Set up a reverse proxy (Nginx or Tailscale Serve)
- Improve service architecture (single entry point)
- Add monitoring (Uptime Kuma)
