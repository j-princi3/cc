| **Port Number** | **Protocol** | **Service / Use Case**                      | **Typical Use**                           |
| --------------- | ------------ | ------------------------------------------- | ----------------------------------------- |
| **22**          | TCP          | SSH (Secure Shell)                          | Remote access to Linux (EC2 Ubuntu)       |
| **80**          | TCP          | HTTP (HyperText Transfer Protocol)          | Unencrypted web traffic (Nginx, websites) |
| **443**         | TCP          | HTTPS (Secure HTTP)                         | Secure web traffic (SSL/TLS)              |
| **3389**        | TCP          | RDP (Remote Desktop Protocol)               | Remote access to Windows (EC2 Windows)    |
| **3000**        | TCP          | Custom / App Servers (e.g., React, Node.js) | Development or app-specific traffic       |
| **8080**        | TCP          | Alternate HTTP (often for testing)          | Proxy servers, local dev servers          |
| **3306**        | TCP          | MySQL Database                              | MySQL DB access                           |
| **5432**        | TCP          | PostgreSQL Database                         | PostgreSQL DB access                      |
| **21**          | TCP          | FTP (File Transfer Protocol)                | File transfers (insecure)                 |
| **23**          | TCP          | Telnet                                      | Remote login (insecure, deprecated)       |
