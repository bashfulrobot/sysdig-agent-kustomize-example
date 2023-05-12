# sysdig-agent-kustomize-example

Quick little POC on adding annotations to the Sysdig Chart

## Add additional kustomization to sysdig-deploy helm charts

In some cases customers might want to add additional not supported by helm charts customizations like adding specific annotations to some of the resources. As an example it might be Hashicorp Vault secret injection by using a Vault Agent Injector and adding specific annotations to the resource.

To achieve this you might use `kustomize` to perform additional modifications of the resources. To achieve that you need to create similar to following files structure in you kustomize directory:

```
kustomization.yaml  - main file used by kustomize to understand what is the source and what kind of kustomizations need to be done.
add-annotations.patch.yaml - file with kustomizations instructions
kustomize   - additional wrapper to save rendered by helm chart manifests and applying kusomize configs.
```

## Prerequisites

- `kustomize` 3.5+ installed in your `PATH`
- A running Kubernetes cluster (or use `helm template` instead of `helm install` in the command below)
- Helm 3.1

## Running the example

```shell
cd platform/kustomize
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
sysdig/sysdig-deploy --create-namespace --post-renderer ./kustomize
```

## References

- [Advanced Helm Techniques - Post Rendering](https://helm.sh/docs/topics/advanced/#post-rendering)
- [Example](https://github.com/thomastaylor312/advanced-helm-demos/tree/master/post-render)
- [Kustomize - patches](https://kubectl.docs.kubernetes.io/references/kustomize/kustomization/patches/)
