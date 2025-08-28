---
title: Terraform
layout: post
categories: eksperiment
---

**Emner:** Automatisering & Scripting · Cloud Computing & DevOps

**Hvorfor disse emner?**  
Jeg ville gøre udvikling og drift reproducerbar og sikker fra dag ét. Automatisering reducerer manuelle fejl, og DevOps/IaC gør det nemt at klargøre miljøer, rulle ændringer ud og rulle dem tilbage igen.

**Forhåndsviden:**  
Lidt Terraform, GitHub Actions, lidt Azure – og generel erfaring med Docker/kontainere.

**Mål:**

- Køre Terraform mod **Azure for Students** uden long‑lived nøgler.  
- Opsætte **OIDC** fra GitHub Actions → Azure (ingen secrets).  
- Lægge **tfstate** i Azure Storage via backend.  
- Få en pipeline der kan **plan/apply** mod en simpel "core"‑infra.  
- Dokumentere fejl, årsag og fix (så jeg kan gentage det senere uden bøvl).

---

## Hvad jeg undersøgte & eksperimenterede med

1) **Bootstrap‑infrastruktur med Terraform**  
Jeg lavede en lille *bootstrap* der opretter:
- Resource Group til state,
- **Storage Account** + container `tfstate` til Terraform‑state,
- **Key Vault** (til senere secrets),
- samt (først) en App Registration til OIDC.

2) **403 på App Registration → ny strategi**  
Ved `terraform apply` fejlede App Registration med:
```
Authorization_RequestDenied: Insufficient privileges to complete the operation
```
Konklusion: Min Entra‑tenant tillod ikke brugeroprettede **App registrations**. For ikke at vente på tenant‑ændringer skiftede jeg strategi til **User‑Assigned Managed Identity (UAMI)** + **Federated Identity Credential (FIC)**, som kan laves via Azure‑ressourcer og RBAC.

3) **OIDC fra GitHub → Azure via UAMI**  
Jeg oprettede en **UAMI** og tilknyttede en **FIC** med `issuer=https://token.actions.githubusercontent.com` og `audience=api://AzureADTokenExchange`. UAMI’en fik rollerne **Contributor** på subscription og **Storage Blob Data Contributor** på storage‑kontoen.

4) **Azure backend til Terraform‑state**  
Jeg satte `backend.hcl` op til at pege på min RG, Storage, container og key (`infra.tfstate`), og slog `use_azuread_auth = true` til.

5) **GitHub Actions med azure/login (OIDC)**  
I workflowet brugte jeg `permissions: id-token: write` og satte repo‑variables:  
`AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID`, `AZURE_CLIENT_ID` (fra UAMI).  
Så kørte jeg `terraform init/plan/apply` mod `infra/` automatisk på push til `main`.



---

## Konfiguration (uddrag)

**Terraform backend (infra/backend.hcl):**
```hcl
resource_group_name  = "rg-<project>-tfstate"
storage_account_name = "st<project><suffix>"
container_name       = "tfstate"
key                  = "infra.tfstate"
use_azuread_auth     = true
```

**Federated Identity Credential (UAMI):**
```hcl
resource "azurerm_user_assigned_identity" "gha" {
  name                = "<project>-gha-umi"
  location            = var.location
  resource_group_name = azurerm_resource_group.tfstate.name
}

resource "azurerm_federated_identity_credential" "gha_exact" {
  name      = "gha-exact-main"
  parent_id = azurerm_user_assigned_identity.gha.id
  issuer    = "https://token.actions.githubusercontent.com"
  audiences = ["api://AzureADTokenExchange"]
  subject   = "repo:Gustavarendd/terraform:ref:refs/heads/main"
}
```

**GitHub Actions (uddrag):**
```yaml
env:
  TF_IN_AUTOMATION: true
  ARM_USE_OIDC: true
  ARM_TENANT_ID: ${{ vars.AZURE_TENANT_ID }}
  ARM_SUBSCRIPTION_ID: ${{ vars.AZURE_SUBSCRIPTION_ID }}
  ARM_CLIENT_ID: ${{ vars.AZURE_CLIENT_ID }}

permissions:
  id-token: write
  contents: read

steps:
  - uses: actions/checkout@v4
  - uses: azure/login@v2
    with:
      tenant-id: ${{ vars.AZURE_TENANT_ID }}
      subscription-id: ${{ vars.AZURE_SUBSCRIPTION_ID }}
      client-id: ${{ vars.AZURE_CLIENT_ID }}
  - uses: hashicorp/setup-terraform@v3
  - run: terraform init -backend-config=backend.hcl
    working-directory: infra
  - run: terraform plan -out=tfplan
    working-directory: infra
  - run: terraform apply -auto-approve tfplan
    if: github.ref == 'refs/heads/main'
    working-directory: infra
```

---

## Fejl & debugging (loguddrag)

**403 ved App Registration:**
```
Authorization_RequestDenied: Insufficient privileges to complete the operation
```
**Årsag**: Tenanten tillader ikke App Registrations for min bruger.  
**Løsning**: Skift til UAMI + FIC eller få rollen *Application Developer*/åbne app‑registreringer.

---

## Refleksioner
- **Sikkerhed**: OIDC fjerner behovet for long‑lived client secrets.  
- **Reproducerbarhed**: tfstate i Azure + CI betyder ens builds lokalt og i pipeline.  
- **Operatørglæde**: Det er nemt at udvide med flere ressourcer (DB, Container Apps) uden at ændre auth‑modellen.

---

## Næste skridt
- Tilføje **Azure Container Apps** (eller AKS) + en lille API ("Planner‑API").  
- **PostgreSQL Flexible Server** + migrations i CI.  
- **Observability**: Grafana/Prometheus/Loki + alerts.  
- **Automation Jobs**: Backup til Blob, synthetics (health checks) og daglig lograpport.  
- **Runbooks/SLO’er** og en lille GameDay‑øvelse (simuler 5xx og genskab).


