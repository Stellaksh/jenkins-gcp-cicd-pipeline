# Jenkins → GCR → GKE CI/CD Pipeline
> Built by [Stellaksh Digital](https://stellaksh.com)

## Pipeline Flow
Code Push --> Jenkins Trigger --> Unit Tests --> Docker Build --> Push to GCR --> Deploy to GKE --> Smoke Test --> Auto-rollback on failure

## Key Features
- Zero-downtime rolling deployments (maxUnavailable: 0)
- Build-number tagged Docker images — full traceability
- Automatic rollback on failed deployment
- GCP service account authentication via Jenkins credentials
- Post-deploy smoke test gate

## Prerequisites
- Jenkins with GCP plugins
- GCP service account key stored as Jenkins credential: `gcp-sa-key`
- GKE cluster running
- GCR enabled on GCP project

## Setup in 3 Steps
1. Add GCP SA key → Jenkins → Credentials → `gcp-sa-key`
2. Update `PROJECT_ID` and `GKE_CLUSTER` in Jenkinsfile
3. Create Jenkins Pipeline job → point to this repo → Build

## Results
- Deployment time: reduced from 2+ hours to under 15 minutes
- Zero manual steps after code push
- Full audit trail via build numbers

---
**Stellaksh Digital** | [stellaksh.com](https://stellaksh.com)
