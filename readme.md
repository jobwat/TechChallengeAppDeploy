# TechChallengeAppDeploy

This is a deployment proposition to Servian's [TechChallengeApp](https://github.com/servian/TechChallengeApp)

## Documentation structure

- [readme.md](readme.md)
  Current file

- [doc/adr](doc/adr) folder
  Architecture Design Records (ADR), details on why can be found in the [first entry](doc/adr/0001-record-architecture-decisions.md)

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

The app may take up to 3-4 minutes to start due to the postgres initialisation time.


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

## Challenge

### Run sheet

- ~~Get postgresql instance with persistance as an Helm chart dependency~~
- ~~Get the app working on Okteto~~
- COMPROMISED - ~~Hook a job to ensure DB creation/seed run only once at install - [doc](https://itnext.io/database-migrations-on-kubernetes-using-helm-hooks-fb80c0d97805#9128)~~
- ~~Get the DB setup to run at container start~~
- ~~Get the main app to wait for postgres dependency to be available - [doc](https://medium.com/geekculture/helm-chart-wait-for-all-dependencies-before-starting-kubernetes-pods-cc0a3ddbf02b)~~
- Get the DB setup to run only at first run


### Known issues

#### Each deployment resets the data

In current implementation the DB setup `updatedb` command is run at each container start.
This is a workaround to the hook/job solution to ensure the DB was setup before the app starts.
The planned hook/job solution doesn't work with a database set as Helm subchart dependency (the dependency gets blocked by the hook (see attempt [here](https://github.com/jobwat/TechChallengeAppDeploy/commit/6c0c8564149755f207dd57ea6675c87dcbeb711a))).

#### Password are hardcoded in cleartext in the repository

This a deliberate choice to simplify this challenge exercise


### Discoveries during this challenge

- Helm with its limitations (with initContainers, hooks and dependencies).
- ADR (Architecture Design Records) concept and implementation.
- Github release helper [ghr](github.com/tcnksm/ghr) to push release with artifacts.

### Implementation nitpick

- [TechChallengeApp/.circleci/config.yml#L147](https://github.com/servian/TechChallengeApp/blob/cd0c072cb11f534dfe1b673b5ec439b91e2d4da9/.circleci/config.yml#L147)
  If current version has already been published, it's currently silenced. I'd output a little message to clarify debugging.
