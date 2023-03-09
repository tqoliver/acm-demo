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

#### Step 1

Manually install OpenShift GitOps operator from GUI

```bash
oc wait --for=condition=Ready deployment/openshift -n openshift-gitops
```

#### Step 2

Update ArgoCD instance with kustomize plugins

```bash
oc apply -f bootstrap/argocd.yaml -n openshift-gitops
```

#### Step 3

Create the Argo application to install ACM instance

```bash
oc apply -f ./hubcluster/argo-applications/acm-install.yaml
```

#### Step 4

Wait for ACM installation and create a new ManagedCluster with `lab-clusterset` label

Create ManagedClusterSet to manage the earlier cluster

Create ManagedClusterSetBinding to binding `clusterset` with namespace

Create a Placement object matching the `clusterset` lab expression with `lab-clusterset`

Create a GitOps cluster object to provide integration with the `lab-clusterset` Placement object and ArgoCD instance

Create a PlacementRule with the dummy cluster as the target to manage configuration specific to that cluster
