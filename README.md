# test-gitops

GitOps desired-state for the pastebin app. Argo CD reconciles each environment
namespace from here; Kargo rewrites the image tags on promotion.

## Layout

```
base/                 # environment-agnostic resources
  backend.yaml        # backend Deployment + Service (Go API, :8080, emptyDir /data)
  frontend.yaml       # frontend Deployment + Service (lighttpd SPA, :8080)
  kustomization.yaml
envs/
  dev/  qa/  prod/    # one overlay per environment
    kustomization.yaml  # sets the namespace + pins the image tags
```

Each `envs/<env>/kustomization.yaml` sets `namespace: <env>` and pins the
`ghcr.io/jmgilman/{backend,frontend}` tags via the kustomize `images:`
transformer. Kargo's promotion step (`kustomize edit set image`) rewrites those
tags as Freight is promoted `dev → qa → prod`.

Render an environment locally with:

```sh
kubectl kustomize envs/dev
```
