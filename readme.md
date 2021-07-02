# TechChallengeAppDeploy

This is a deployment proposition to Servian's [TechChallengeApp](https://github.com/servian/TechChallengeApp)

## Documentation structure

- [readme.md](readme.md)
  Current file

- [doc/adr](doc/adr) folder
  Architecture Design Records (ADR), details on why can be found in the [first entry](adr/0001-record-architecture-decisions.md)

  Naming convention: `####-<decision title>` where the first 4 digits are iterated by 1 for each record.


## Deployment

The deployment of the app is made using an Helm chart ([helm_charts/tech-challenge-app](helm_charts/tech-challenge-app) folder)

More on Helm at https://helm.sh/

### Deploy Requirements

- Have access to a kubernetes cluster with admin permissions

  A free option is to spin up a Kubernetes cluster on http://okteto.com.

- Have the Kubernetes cluster access set up on your system.

  A way is to point to the kube.config Yaml file via an environment variable. ([okteto doc page](https://okteto.com/docs/cloud/credentials/index.html#download-your-kubernetes-credentials-from-the-okteto-cloud-ui))

  Sample `.env` file

  ```bash
  KUBECONFIG=/Users/<your_account_name>/.kube/<your-cluster>-kube.config
  ```


### Pre-Deploy

Bundle up the postgresql helm package dependency

```
helm dep update ./helm_charts/tech-challenge-app
```


### Actual deploy

Deploy on default namespace (great on a test account like okteto)

```
helm install tech-challenge-app ./helm_charts/tech-challenge-app
```

Or create a temporary namespace (recommended if multiple projects on the cluster)

```
helm install tech-challenge-app --namespace <your_namespace> --create-namespace ./helm_charts//tech-challenge-app
```


### Access the app

By default the app will be accessible at tech-challenge-app.<your_cluster>

With okteto: go to https://tech-challenge-app-<okteto_username>.cloud.okteto.net/

Oneliner:

```
open "http://`kubectl get ingress -o jsonpath="{.items[0].spec.rules[0].host}"`"
```


### Cleanup

Helm `uninstall` will remove most resources but the persisted volume of the database.

```
helm uninstall --namespace <your_namespace> tech-challenge-app
```

To remove the persisted volume, run the following

```
kubectl --namespace <your_namespace> delete persistentvolumeclaims --all
```

## TODO

- ~~Get postgresql instance with persistance as dependency~~
- ~~Get the app working on Okteto~~
- Hook a job to ensure DB migration/creation/seed run at install/upgrade - [doc](https://itnext.io/database-migrations-on-kubernetes-using-helm-hooks-fb80c0d97805#9128)
- Get the main app to wait for postgres dependency to be available - [doc](https://medium.com/geekculture/helm-chart-wait-for-all-dependencies-before-starting-kubernetes-pods-cc0a3ddbf02b)


## Chatter

### Happy discoveries during this challenge

- ADR (Architecture Design Records) concept and implementation
- Github release helper [ghr](github.com/tcnksm/ghr) to push release with artifacts

### Points I'd make clearer

- [.circleci/config.yml#L147](https://github.com/servian/TechChallengeApp/blob/cd0c072cb11f534dfe1b673b5ec439b91e2d4da9/.circleci/config.yml#L147)
  If current version has already been published, it's currently silenced. I'd output a little message to clarify debugging.
