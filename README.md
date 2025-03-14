# Kyverno

Kyverno is a Kubernetes-native policy engine designed to manage and enforce policies for Kubernetes resources. It allows administrators to define, validate, mutate, and enforce policies using Kubernetes Custom Resource Definitions (CRDs). 

## Key Features of Kyverno:
1. **Kubernetes-Native**:
   - Policies are managed as Kubernetes resources, making it easier for Kubernetes users to adopt and integrate with existing workflows.
   
2. **Policy Types**:
   - **Validation Policies**: Ensure that resource configurations meet certain criteria.
   - **Mutation Policies**: Automatically modify resource configurations to align with desired states.
   - **Generation Policies**: Create or update resources like ConfigMaps or Secrets dynamically.
   - **Cleanup Policies**: Delete resources like bare pods.
   - **Verify Policies**: Enforces usage of signed resources.

3. **Simple and Declarative**:
   - Policies are written in YAML, making them simple and intuitive for Kubernetes users without requiring a new domain-specific language (DSL).

4. **Admission Control and Continuous Enforcement**:
   - Kyverno operates as an admission controller, intercepting resource creation, modification, and deletion requests to enforce policies.
   - It can also run in audit mode, continuously scanning existing resources for compliance.

5. **Policy Categories**:
   - Security: Enforce security best practices (e.g., ensuring containers run as non-root).
   - Operations: Standardize resource configurations (e.g., adding labels or annotations).
   - Governance: Enforce compliance requirements (e.g., verifying image signatures).

6. **Built-in Policy Library**:
   - Kyverno provides a collection of ready-to-use policies for common use cases, such as restricting privileged containers or ensuring namespaces have resource quotas.

7. **Integration with Kubernetes Ecosystem**:
   - Kyverno integrates seamlessly with Kubernetes and tools like Helm, enabling dynamic policy application based on context.

8. **Flexible Policy Execution**:
   - Policies can be applied globally (cluster-wide) or scoped to specific namespaces.

### Example Use Case
To enforce that all Pods must have a specific label:

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-pod-label
spec:
  validationFailureAction: enforce
  rules:
  - name: check-label
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Pods must have the label 'app'."
      pattern:
        metadata:
          labels:
            app: "*"
```

## Benefits of Using Kyverno
- **Kubernetes-Centric**: No need to learn a new language; policies are expressed in Kubernetes YAML.
- **Ease of Use**: Intuitive and declarative policies with minimal complexity.
- **Extensibility**: Supports dynamic and context-aware policies.
- **Auditing and Reporting**: Identify non-compliant resources without enforcing changes.

Kyverno is often compared to tools like Open Policy Agent (OPA) with Gatekeeper, but its Kubernetes-native approach and simplicity make it a preferred choice for many Kubernetes users.

## Install
Install using [Helm or via YAMLs](https://kyverno.io/docs/installation/)
```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
```
```
helm install kyverno kyverno/kyverno -n kyverno --create-namespace

NAME: kyverno
LAST DEPLOYED: Fri Mar 14 11:33:26 2025
NAMESPACE: kyverno
STATUS: deployed
REVISION: 1
NOTES:
Chart version: 3.3.7
Kyverno version: v1.13.4

Thank you for installing kyverno! Your release is named kyverno.

The following components have been installed in your cluster:
- CRDs
- Admission controller
- Reports controller
- Cleanup controller
- Background controller


⚠️  WARNING: Setting the admission controller replica count below 2 means Kyverno is not running in high availability modee.


⚠️  WARNING: PolicyExceptions are disabled by default. To enable them, set '--enablePolicyException' to true.

💡 Note: There is a trade-off when deciding which approach to take regarding Namespace exclusions. Please see the documentation at https://kyverno.io/docs/installation/#security-vs-operability to understand the risks.
```
```
kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.13.0/install.yaml
kubectl get po -n kyverno
kubectl explain policy.spec
```
Check webhook configurations
```
kubectl get mutatingwebhookconfigurations
kubectl get mutatingwebhookconfigurations kyverno-resource-mutating-webhook-cfg -o yaml
kubectl get validatingwebhookconfigurations
kubectl get validatingwebhookconfigurations kyverno-resource-validating-webhook-cfg -o yaml 
```
ClusterPolicy and Policy
```
kubectl get cpol,pol -A  
```

## Examples

### Enforce labels
```
kubectl apply -f examples/enforce_require_labels.yaml 
kubectl run nginx --image nginx --labels team=backend
```

```
kubectl get policyreport -o wide   
NAME                                   KIND   NAME    PASS   FAIL   WARN   ERROR   SKIP   AGE
89f7d4bd-525c-4b57-a79d-70b25971e0c2   Pod    nginx   1      0      0      0       0      32s
```

```
kubectl delete po nginx
kubectl delete clusterpolicy require-labels
```

### Mutate labels
```
kubectl apply -f examples/mutate_add_labels.yaml
kubectl run nginx --image nginx
```
```
kubectl get po nginx --show-labels
NAME    READY   STATUS    RESTARTS   AGE   LABELS
nginx   1/1     Running   0          18s   run=nginx,team=devops
```

```
kubectl delete po nginx
kubectl delete clusterpolicy add-labels
```              

## Known errors

Admission webhook "kyverno-cleanup-controller.kyverno.svc" denied the request: cleanup controller has no permission to delete kind Pod.
```
kubectl edit clusterrole kyverno:cleanup-controller:core
```
and add the below permissions

```yml
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - delete
  - list
  - get
```

## Links
- https://kyverno.io/#about-kyverno
- https://kyverno.io/docs/
- https://kyverno.io/policies/
- https://playground.kyverno.io/
- https://kyverno.io/blog/
- https://github.com/kyverno/kyverno/
- https://training.linuxfoundation.org/certification/kyverno-certified-associate-kca/
- https://grafana.com/docs/grafana/latest/setup-grafana/installation/kubernetes/
- https://grafana.com/docs/grafana/latest/setup-grafana/installation/helm/


## Exam topics

### Fundamentals of Kyverno  
- Kyverno Policies & Rules: https://kyverno.io/docs/kyverno-policies/
- YAML Manifests  
- Admission Controllers  
- OCI Images  

### Installation, Configuration, and Upgrades  
- Helm-based Installation and Configuration  
- Kyverno Custom Resource Definitions (CRDs)  
- Controller Configuration with Flags  
- Configuring Kyverno RBAC, roles, and permissions  
- High Availability Installations  
- Upgrading Kyverno  

### Kyverno CLI
- Installing Kyverno CLI: https://kyverno.io/docs/kyverno-cli/
- apply: https://kyverno.io/docs/kyverno-cli/usage/apply/
- test: https://kyverno.io/docs/kyverno-cli/usage/test/  
- jp  

### Applying Policies  
- Applying Policy in Cluster  
- Resource Selection  
- Common Policy Settings for Kyverno Rules  

### Writing Policies  
- Validation Rules: https://kyverno.io/docs/writing-policies/validate/  
- Preconditions  
- Background Scans  
- Mutation Rules  
- Generation Rules  
- VerifyImage Rules  
- Variables & API Calls in Policies  
- JSON Patches  
- Autogen Rules  
- Cleanup Policies: https://kyverno.io/docs/writing-policies/cleanup/#cleanup-policy 
- Common Expression Language (CEL)  

### Policy Management  
- Policy Reports  
- PolicyExceptions  
- Kyverno Metrics  
