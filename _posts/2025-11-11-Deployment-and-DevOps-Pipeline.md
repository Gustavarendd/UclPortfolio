---
title: Deployment og DevOps Pipeline
layout: post
categories: Diagram
---


{% include mermaid.html %}
``` mermaid
graph TD

%% === DevOps Pipeline ===

G1[Developer commits code to GitHub Repository] --> G2[GitHub Actions CI CD Pipeline]

G2 -->|Build and push Docker images| G3[Docker Hub or Container Registry]

G3 -->|Pull latest image| G4[VPS Server Docker Host]

G4 --> VPS_Server

C[Nginx Reverse Proxy]

D[Docker Container NET Backend API]

E[Docker Container MSSQL Database]

  

%% === Runtime Network Flow ===

A1[Web Frontend React App] -->|HTTPS| B[Cloudflare Proxy DNS and SSL]

A2[Mobile App Flutter Android] -->|HTTPS| B

A3[Mobile App Swift iOS] -->|HTTPS| B

B -->|HTTPS 443| C

C -->|HTTP 8080| D

D -->|TCP 1433| E

  

%% === Secure Database Access ===

F[SSMS Client Local Machine] -. SSH Tunnel .-> E

  

%% === Grouping ===

subgraph Cloudflare_Network [Cloudflare Network]

B

end

  

subgraph VPS_Server [VPS Server Docker Host]

C

D

E

end

  

subgraph DevOps_Pipeline [DevOps Pipeline]

G1

G2

G3

G4

end

  

%% === Styling ===

classDef client fill:#E3F2FD,stroke:#1E88E5,color:#000

classDef proxy fill:#E8F5E9,stroke:#43A047,color:#000

classDef backend fill:#FFF3E0,stroke:#FB8C00,color:#000

classDef db fill:#FCE4EC,stroke:#C2185B,color:#000

classDef devops fill:#E1F5FE,stroke:#0277BD,color:#000

  

class A1,A2,A3,F client

class B,C proxy

class D backend

class E db

class G1,G2,G3,G4 devops
```
graph TD

