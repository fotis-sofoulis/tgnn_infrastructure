# Setting up the infrastructure

## Prerequisites

- [ ] A kubernetes cluster with at least 3 worker nodes and version 1.26 or higher

## Install Argo

### Install argo cli

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Expose argo ui

```bash
kubectl apply -n argocd -f app-constructs/argocd/ingress.yaml
kubectl apply -n argocd -f app-constructs/argocd/argocd-cmd-params-patch.yaml
kubectl rollout restart deployment -n argocd argocd-server
```

### Add argocd host to your hosts file

You will have to add `argocd.tgnnapp.local` to your hosts file and point it to one of your cluster's worker nodes.

### Obtain argo password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Login to argo

You can now login to argo at `https://argocd.tgnnapp.local` using the password obtained in the previous step. You should also update the password, but it is not necessary.

### Add the repo to the repositories list

From argo ui, go to `Settings` > `Repositories` and click on `CONNECT REPO` button. You'll be asked the following 4 questions:

Name: `tgnn_infrastructure`
Project: `default`
Repository URL: `git@github.com:fotis-sofoulis/tgnn_infrastructure.git`
SSH Private Key: copy the contents of your private key file and paste them in the text area

After that, click on `CONNECT` button and you should see the repo in the list. This means that argo can now access the repo and deploy the apps.

### Apply the root app

From argo ui, go to `Applications` and click on `NEW APP` button. Then, click on `YAML` tab and paste the contents of `app-root.yaml` file. After that, hit `SAVE` then `CREATE` and you should see the app in the list. app-root is configured to scan the contents of `app-directory` for argo apps and deploy them automatically. You'll notice that as time passes, more apps will be deployed as they are detected by argocd.

## Additional hosts we need to add to our hosts file

```
argocd.tgnnapp.local (already mentioned above)
longhorn.tgnnapp.local
spark.tgnnapp.local
... more to come ...
```