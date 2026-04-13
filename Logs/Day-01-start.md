# Day 01 - Ubuntu server installation

## Objective
Set up a dedicated machine to start building a personnal home lab.

---

## What i did 

### Machine setup
- Used an old pc as a server
- Created an bootable usb key with Ubuntu server

---

### OS installation
- Installed Ubuntu server on the machine
- Configured :
  - System language and keyboard
  - hostname
  - main user account
- Chose the minimal installation (no graphical intarface)

---

### Network
- Used automatic network configuration (DHCP)
- Verified that the server is connected to the local network

---

## What i understood 
- A server does not require a graphical interface, you can use it terminal only
- The machine recieves a local IP address from the router to communicate on the network

---

## Issues
- Had to access the BIOS to boot from the usb key
  
--- 

## Next steps
- Configure remote access using ssh
- Set up basic security (users, firewall)
