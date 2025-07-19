## Gitea Deployment with Persistent Storage and External MySQL Database
## Overview
## This project deploys **Gitea** using the Helm chart on Kubernetes, with:

- Persistent storage for repository data
- External MySQL database for production-grade data handling
- Public exposure of the Gitea UI using **kubectl port-forward** and **ngrok**
Prerequisites
- Kubernetes cluster with `kubectl` configured
- Helm installed
- Ansible installed (if using automation)
- Ngrok installed and authtoken configured
- Access to a public terminal or Codespaces

## Step 1: Deploy MySQL Database
We use the Bitnami MySQL Helm chart to create an external database for Gitea:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install mysql bitnami/mysql --set auth.rootPassword=my-root-password,auth.database=gitea
```

*Replace `my-root-password` with a strong password.*
Step 2: Deploy Gitea with Persistent Storage and External Database
Create a `values.yaml` file with the following content:
```yaml
persistence:
  enabled: true
  size: 10Gi
  storageClassName: standard

database:
  type: mysql
  host: mysql.default.svc.cluster.local
  name: giteaname
  user: giteauser
  password: giteapass
```

## Deploy Gitea Helm chart using this file:
```bash
helm repo add gitea-charts https://dl.gitea.io/charts/
helm install gitea gitea-charts/gitea -f values.yaml
```

## Step 3: Verify Deployments and Persistent Volumes
```bash
kubectl get pods -n default
kubectl get pvc -n default
```

Ensure all pods are running and PVC is bound.
## Step 4: Expose Gitea Locally and Publicly
Forward Gitea HTTP port to your local machine:
```bash
kubectl port-forward svc/gitea-http 3000:3000
```

Open another terminal and start ngrok tunnel:
```bash
ngrok http 3000
```

Copy the generated `https://*.ngrok-free.app` URL.
## Step 5: Access Gitea
Open your browser and navigate to the ngrok URL. You should see the Gitea UI.
Notes
- Remember to add your ngrok URL to Gitea’s `ROOT_URL` in the Helm `values.yaml` for proper links.
- Store secrets securely (avoid hardcoding passwords in production).
- This setup is suitable for development and demo purposes.
Submission
- Include screenshots showing running pods, PVCs, ngrok public URL, and Gitea UI.
- Add the ngrok public URL to `comment.txt`.
- Provide this README file with detailed instructions.
Contact
For questions, contact: Durlabh Tilavat (#8938972)
