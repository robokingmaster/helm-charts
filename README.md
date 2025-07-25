# helm-charts
Helm charts for application deployments

### Package all The Charts
```
mkdir -p packaged
for dir in charts/*; do
  helm package "$dir" -d packaged
done

```

### Generating Indexed
```
helm repo index packaged --url https://your-username.github.io/helm-charts/packaged

```

### Commit and push
```
git add all
git commit -m "Add packaged charts and index"
git push origin main

```

