# argocd-open-liberty-operator

## How to deploy Open Liberty Operator with an ArgoCD Application
1. Install the OpenShift GitOps operator in OpenShift UI into the `openshift-gitops` namespace.
2. Grant cluster-admin permissions for the GitOps operator to be able to create CRDs for Open Liberty Operator.
```
oc adm policy add-cluster-role-to-user cluster-admin  system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n openshift-gitops
```
3. Choose the Kustomize artifacts folder to pull from the [Open Liberty Operator releases page](https://github.com/OpenLiberty/open-liberty-operator/tree/main/deploy/releases). In this example, I use Kustomize files from `deploy/releases/1.4.3/kustomize/base`.
3. You can choose to modify the default namespace(s) by changing `.spec.source.kustomize.namespace` in the `Application` custom resource(s).
4. Install Open Liberty Operator
  - To install for `OwnNamespace` mode (default operator/watched namespace is `open-liberty`):
   `oc apply -f kustomize-open-liberty-operator-own-namespace`
  - To install for `AnotherNamespace` mode (default operator namespace is `olo-ns` and watched namespace is `olo-watched-ns`):
  `oc apply -f kustomize-open-liberty-operator-another-namespace`
  - To install for `AllNamespaces` mode (default operator namespace is `open-liberty` and watching all namespaces):
  `oc apply -f kustomize-open-liberty-operator-all-namespaces`
5. (Optional) Ignore runtime artifacts generated by Open Liberty Operator such as the `open-liberty-operator` ConfigMap by editing the Application custom resource.
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: open-liberty-operator
  namespace: openshift-gitops
spec:
...
  ignoreDifferences:
  - group: ''
    kind: ConfigMap
    name: open-liberty-operator
```