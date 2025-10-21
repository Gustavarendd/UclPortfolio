---
title: Github -> container -> VPS pipeline
layout: post
categories: eksperiment
---


**Emner:** Automatisering & Scripting · Cloud Computing & DevOps  

**Hvorfor disse emner?**  
Disse to områder er direkte forbundet i moderne softwareudvikling. Automatisering reducerer manuelle fejl og sikrer ensartede processer, mens Cloud / DevOps-principper giver mulighed for skalerbare, driftssikre og hurtigt leverbare løsninger.  
I denne session blev der arbejdet med en fuld CI/CD-pipeline, der automatiserer build, test og deployment af et .NET-projekt via GitHub Actions og Docker på en VPS.

---

**Forhåndsviden:**  
- Erfaring med scripting (Bash, Python, JavaScript)  
- Grundlæggende forståelse for Docker og containere  
- Kendskab til Git og GitHub Actions  
- Erfaring med Cloud-drift (Azure / AWS / VPS)

---

**Mål:**  

- Automatisere build- og release-processer via GitHub Actions  
- Bygge og publicere Docker-images til GitHub Container Registry (GHCR)  
- Implementere sikker, automatisk deployment til en VPS  
- Forstå forskellen mellem `docker build` og Buildx, samt løse “context not found”-fejl  
- Arbejde med miljøvariable, secrets og branch-betinget deployment  
- Fejlsøge .NET 8 build-trinene i Docker og optimere Dockerfile-opsætningen  

---

**Proces og læringspunkter:**  

1. **Opsætning af CI/CD-pipeline**  
   - Workflow udløses ved push til `master` branch.  
   - Bygger og pusher et image til GHCR.  
   - Udruller automatisk til VPS via SSH.  

2. **Løsning af fejl:**  
   - *`context "." not found`* → fjernet Buildx-afhængighed og brugt klassisk `docker build`.  
   - *`invalid reference format`* → repo-navn gjort lowercase og fjernet trailing “-”.  
   - *`.NET publish exit 1`* → tilføjet `ARG BUILD_CONFIGURATION` inde i build-stage.  

3. **Resultat:**  
   - Stabil, reproducerbar pipeline uden afhængighed af Buildx-contexts.  
   - Automatisk versionering med både `latest` og `sha-<commit>` tags.  
   - Enkelt deployment-flow, hvor `master` alene trigger VPS-deployment.

---





{% include mermaid.html %}

```mermaid
graph TD
  A[GitHub Push] --> B[Build & Tag Docker Image]
  B --> C[Push to GHCR]
  C --> D[SSH to VPS]
  D --> E[Pull & Run Container]
  ```
