# ACM Demo

Deploy an application on the OpenShift cluster using ArgoCD and ACM.

## Terminology and Concepts

With the 2.3 version of Advanced Cluster Management, a new `gitopscluster` resource is introduced, which connects to a placement resource to determine which clusters to import into Argo CD. This feature allows deploying applications to a set of common clusters (i.e., lab, non-prod, or prod) or a specific cluster with a cluster name.

### Terminologies

**ManagedCluster:** ManagedCluster defines the state of the cluster

**ManagedClusterSet:** A ManagedClusterSet is a group of managed clusters. A managed cluster cannot exist in more than one ManagedClusterSet

**ManagedClusterSetBinding:** A ManagedClusterSetBinding resource binds a ManagedClusterSet resource to a namespace

**Placement:** Defines a rule to select a set of ManagedClusters from the ManagedClusterSets, bound to the placement namespace

**GitOpsCluster:** Provides integration with ArgoCD and PlacementRef

**PlacementRule:** Defines the target clusters for deployment

Manually install OpenShift GitOps operator from GUI

```bash
oc wait --for=condition=Ready deployment/openshift -n openshift-gitops
```

Update ArgoCD instance with kustomize plugins; Create new group `cluster-admins`

```bash
oc apply -f bootstrap/argocd.yaml -n openshift-gitops
oc adm policy add-cluster-role-to-user cluster-admin -z openshift-gitops-argocd-application-controller -n openshift-gitops
oc adm policy add-cluster-role-to-group cluster-admin cluster-admins
```

Wait for ACM installation; create managed cluster resources via Argo application

```bash
oc apply -f ./hubcluster/argo-applications/acm-manage-setup.yaml
```

Create Managed cluster application set

```bash
oc apply -f ./hubcluster/argo-applications/managedclusters-lab-applicationset.yaml
```
