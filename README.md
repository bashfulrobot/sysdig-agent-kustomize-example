# sysdig-agent-kustomize-example

Quick little POC on adding annotations to the Sysdig Chart

## Add additional kustomization to sysdig-deploy helm charts

Sometimes, a customer may want to add additional not supported by helm charts customizations, like adding specific annotations to some of the resources. For example, it might be Hashicorp Vault secret injection using a Vault Agent Injector and adding particular annotations to the resource.

To achieve this, you might use `kustomize` to perform additional modifications of the resources. To accomplish that, you need to create a similar to the following files structure in your kustomize directory:

- `kustomization.yaml`  - the main file used by kustomize to understand the source and what kind of customizations need to be done.
- `add-annotations.patch.yaml` - file with the instructions for kustomize.
- `kustomize.sh`   - additional wrapper to save a rendered helm chart manifest and apply the kusomize configs.

## Prerequisites

- `kustomize` 3.5+ installed in your `PATH`
- A running Kubernetes cluster (or use `helm template` instead of `helm install` in the command below)
- Helm 3.1

## Running the example

```shell
helm upgrade --install sysdig-agent --namespace sysdig-agent \
--set global.sysdig.accessKey=MY_ACCESS_KEY \
--set global.sysdig.secureAPIToken=MY_TOKEN \
--set global.sysdig.region=us2 \
--set global.kspm.deploy=true \
--set global.clusterConfig.name=lima-k8s \
--set admissionController.enabled=true \
--set admissionController.scanner.enabled=false \
--set nodeAnalyzer.secure.vulnerabilityManagement.newEngineOnly=true \
--set nodeAnalyzer.nodeAnalyzer.benchmarkRunner.deploy=false \
--set nodeAnalyzer.nodeAnalyzer.hostScanner.deploy=true \
--set nodeAnalyzer.nodeAnalyzer.hostScanner.scanOnStart=true \
--set nodeAnalyzer.nodeAnalyzer.runtimeScanner.settings.eveEnabled=true \
sysdig/sysdig-deploy --create-namespace --post-renderer ./kustomize.sh
```

- *NOTE* - you can also leverage a `values.yaml` with `helm` as well.

## References

- [Advanced Helm Techniques - Post Rendering](https://helm.sh/docs/topics/advanced/#post-rendering)
- [Example](https://github.com/thomastaylor312/advanced-helm-demos/tree/master/post-render)
- [Kustomize - patches](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/patches/)
