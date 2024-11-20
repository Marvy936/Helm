
# Helm Notes

## Table of Contents
1. [General Commands](#general-commands)
2. [Helm Hub](#helm-hub)
3. [Helm Repo](#helm-repo)
4. [Helm Release](#helm-release)
5. [Helm Upgrade and Rollback](#helm-upgrade-and-rollback)
6. [Helm Create and Package](#helm-create-and-package)
7. [Mustache Example](#mustache-example)
8. [Helm Dependencies](#helm-dependencies)
9. [Helm Subcharts](#helm-subcharts)
10. [Helm Plugins](#helm-plugins)

---

## General Commands

```bash
helm version                              # Shows installed version of Helm.
```

---

## Helm Hub

```bash
helm search hub                           # Lists all charts on the Helm Hub.
helm search hub nginx                     # Lists all charts using nginx on the Helm Hub.
artifacthub.io                            # Hub website for manual chart search.
```

---

## Helm Repo

```bash
helm repo list                            # Lists available repositories.
helm repo add bitnami https://charts.bitnami.com/bitnami # Adds the Bitnami repository (from artifacthub.io).
helm repo update                          # Updates the local cache after adding repositories.
helm search repo                          # Lists available packages in repositories.
--regexp "\vbitnami/nginx\v"            # Searches using regex.
helm search repo nginx                    # Searches for nginx in repositories.
--versions                                # Lists all available versions.
helm repo remove {repo_name}             # Removes a repository.
helm show chart/readme/values/all/crds bitnami/nginx # Shows specific details of a chart.
```

---

## Helm Release

```bash
helm pull bitnami/nginx                   # Pulls the package from the repository.
helm install {name_of_release} bitnami/nginx # Installs the package as a release with a specified name.
--version 13.2.0                          # Installs a specific version of the chart.
--set replicaCount=2                      # Sets replicas to 2 (or another configuration).
--values values.yaml                      # Reads configuration from a file.
helm list                                 # Lists all releases.
kubectl get all                           # Checks the release in Kubernetes.
helm status {name_of_release}            # Shows the DNS address of the release.
helm uninstall {name_of_release}         # Uninstalls the release.
helm get manifest {name_of_release}      # Displays the manifest of the release.
```

---

## Helm Upgrade and Rollback

```bash
helm upgrade {name_of_release} bitnami/nginx --version 13.1.3 # Upgrades a release to a new version.
--set replicaCount=2                                          # Sets replicas to 2 or other configurations.
--values values.yaml                                          # Reads configuration from a file.
helm rollback {name_of_release} 1                            # Rolls back to revision 1.
helm history {name_of_release}                               # Displays the history of changes for a release.
```

---

## Helm Create and Package

```bash
helm create {name}                          # Creates empty templates for a Helm package.
helm package {name}                         # Creates a package (tgz file).
--version 0.3.0                             # Packages as a new version and updates Chart.yaml.
helm template .                             # Displays the manifest of the current package.
-s templates/service.yaml                   # Displays only the service manifest.
```

---

## Mustache Example

### Values File (`values.yaml`)
```yaml
part: 2
service:
   port: 80
```

### Deployment File (`deployment.yaml`)
```yaml
-image: bletvaska/rambo:{{ .Values.part }}
```

### Service File (`service.yaml`)
```yaml
-port: {{ .Values.service.port }}
```

### Explanation
- `.Values`: Extracts values from the `values.yaml` file.
- `.service`: Refers to the `service` section in the YAML.
- `.port`: Extracts the value `80` under `service.port`.

---

## Helm Dependencies

### Adding Dependencies to `Chart.yaml`

```yaml
apiVersion: v2
name: wordpress-deps
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: wordpress
  repository: "https://charts.bitnami.com/bitnami"
  version: "23.1.23"
- name: adminer
  repository: "https://cetic.github.io/helm-charts"
  version: "0.2.1"
```

### Commands

```bash
helm dependency list .                        # Lists all chart dependencies.
helm dependency update .                      # Updates dependencies for the chart.
helm package {name} --dependency-update      # Packages the chart with resolved dependencies.
```

---

## Helm Subcharts

### Creating Subcharts

```bash
helm create umbrella-chart                    # Creates a main chart directory.
# Add subcharts in the `charts` directory. They will be released with the main chart.
```

### Configuring Values for Subcharts

In `values.yaml`:

```yaml
wordpress:                                    # Subchart name (directory name).
  replicaCount: 3

adminer:                                      # Another subchart.
  replicaCount: 2

# Main chart configuration:
replicaCount: 2
```

---

## Helm Plugins

```bash
helm plugin install https://github.com/databus23/helm-diff  # Installs the Helm Diff plugin.

helm diff upgrade {release_name}                            # Shows differences before upgrading.

helm diff revision {release_name} 1 2                       # Compares revisions 1 and 2.

helm plugin uninstall {name}                                # Uninstalls a plugin.
```
