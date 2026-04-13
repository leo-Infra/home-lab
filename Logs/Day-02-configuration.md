# Day 02 - Server configuration

## Objective
Set up the basic configuration of the server ater installing Ubuntu.
---

## What i did 

### Users management
- Created additional user(s), to practice and improve security
- Avoided using root account directly
- Tested user switching and permissions

---

### Firewall setup
- Installed and enabled a firewall using 'ufw'
- Applied default rules:
  - deny incoming connections
  - allow outgoing connections
- Opened port 22 to allow SSH acces
- Checked firewall rules and status

---

### SSH configuration for remote access
- Installed and enable OpenSSH
- Allowed ssh connections through the firewall by opening port 22 

---

## What i understood 
- A server must be deny by default to minimize risks as much as possible
- A firewall filters network traffic by blocking or allowing connections

---

## Questions
- Need a deeper understanding of:
  - how ports are used by services
  - how firewall rules interact with running services 
  
--- 

## Next steps
- Configure some services (VPN, Cloud, ...)
- Configure SSH key for better security
