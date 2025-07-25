# helm-charts
Helm charts for application deployments

### Publishing charts in github

#### Prepare Your Helm Charts
Organize your charts in a directory structure like this:
```
my-helm-repo/
├── charts/
│   ├── chart-one/
│   └── chart-two/
```
Each chart folder should follow the standard Helm chart structure (Chart.yaml, values.yaml, templates/, etc.).

#### Package the Charts
Use the Helm CLI to package each chart:

```
cd charts
helm package chart-one
helm package chart-two
```
This will generate .tgz files like chart-one-0.1.0.tgz.

#### Create an index.yaml File
Generate the Helm repository index file:
```
helm repo index . --url https://<your-github-username>.github.io/<repo-name>
```
Replace the URL with the GitHub Pages URL where the repo will be hosted.

move tgz and index.yaml file to home directory of repository

### Commit and push
```
git add all
git commit -m "Add packaged charts and index"
git push origin main
```

#### Enable GitHub Pages
Go to your GitHub repository settings:

Navigate to Pages.
Set the source to the gh-pages branch and root directory.
GitHub will provide a URL like:
https://<your-username>.github.io/<repo-name>/

#### Add the Repo to Helm
On any machine, you can now add your repo:
```
helm repo add myrepo https://<your-username>.github.io/<repo-name>/
helm repo update

helm search repo myrepo
```

#### Install Charts
Retrive the values file and update accordingly. 

```
helm inspect values myrepo/colorapp > colorapp.yaml
```
We can also add ACM certificate in annotation as
```
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: colorapp-ingress
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/healthcheck-path: /health
    alb.ingress.kubernetes.io/group.name: frontend
    alb.ingress.kubernetes.io/certificate-arn: <ACM ARN Endpoint>
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS": 443}]'
spec:
  ingressClassName: alb
  rules:
    - host: colorapp.example.com
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: service-red
              port:
                number: 80
```
Using this values file lets install the helm chart
```
helm install colorapp myrepo/colorapp --namespace colorapp --values colorapp.yaml
```

#### Uninstall Chart
```
helm uninstall colorapp --namespace colorapp
helm repo remove myrepo
```